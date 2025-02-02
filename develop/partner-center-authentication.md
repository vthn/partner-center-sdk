---
title: Partner Center authentication
description: Partner Center uses Azure AD for authentication, and to use the Partner Center APIs you must configure your authentication settings correctly.
ms.assetid: 2307F2A8-7BD4-4442-BEF7-F065F16DA0B2
ms.date: 08/09/2019
ms.localizationpriority: medium
---

# Partner Center authentication

Applies to:

- Partner Center
- Partner Center operated by 21Vianet
- Partner Center for Microsoft Cloud Germany
- Partner Center for Microsoft Cloud for US Government

Partner Center utilizes Azure Active Directory for authentication. When interacting with the Partner Center API, SDK, or PowerShell module you must correctly configure an Azure AD application and then request an access token. Access tokens obtained using app only or app + user authentication can be used with the Partner Center. However, there are two important items that need to be considered

- You must utilize multi-factor authentication when accessing the Partner Center API using app + user authentication. To find more information regarding this change, see [Enable secure application model](enable-secure-app-model.md)
- Not all of the operations the Partner Center API support app only authentication. This means there certain scenarios where you will be required to use app + user authentication. Under the *Prerequisites* heading on each [Scenario](https://docs.microsoft.com/partner-center/develop/scenarios) article, you will find documentation that states whether app only authentication, app + user authentication, or both are supported.

## Initial setup

1. To begin, you need to make sure that you have both a primary Partner Center account, and an integration sandbox Partner Center account. For more information, see [Set up Partner Center accounts for API access](set-up-api-access-in-partner-center.md). Make note of the Azure AAD App registration ID and Secret (client secret is required for App only identification) for both your primary account and your integration sandbox account.

2. Sign in to Azure AD from the Azure management portal. In **permissions to other applications**, set permissions for **Windows Azure Active Directory** to **Delegated Permissions**, and select both **Access the directory as the signed-in user** and **Sign in and read user profile**.

3. In the Azure management portal, **Add application**. Search for "Microsoft Partner Center", which is the Microsoft Partner Center application. Set the **Delegated Permissions** to **Access Partner Center API**. If you are using Partner Center for Microsoft Cloud Germany or Partner Center for Microsoft Cloud for US Government, this step is mandatory. If you are using Partner Center global instance, this step is optional. CSP Partners can use the App Management feature in the Partner Center portal to bypass this step for Partner Center global instance.

## App-only authentication

If you would like to use app-only authentication to access the Partner Center REST API, .NET API, Java API, or PowerShell module then you can do so by leveraging the following:

### [.NET](#tab/apponly-dotnet)

```csharp
public static IAggregatePartner GetPartnerCenterTokenUsingAppCredentials()
{
    IPartnerCredentials partnerCredentials =
        PartnerCredentials.Instance.GenerateByApplicationCredentials(
            PartnerApplicationConfiguration.ApplicationId,
            PartnerApplicationConfiguration.ApplicationSecret,
            PartnerApplicationConfiguration.ApplicationDomain);

    // Create operations instance with partnerCredentials.
    return PartnerService.Instance.CreatePartnerOperations(partnerCredentials);
}
```

### [Java](#tab/apponly-java)

```java
public IAggregatePartner getAppPartnerOperations()
{
    IPartnerCredentials appCredentials =
        PartnerCredentials.getInstance().generateByApplicationCredentials(
        PartnerApplicationConfiguration.getApplicationId(),
        PartnerApplicationConfiguration.getApplicationSecret(),
        PartnerApplicationConfiguration.getApplicationDomain());

    return PartnerService.getInstance().createPartnerOperations( appCredentials );
}
```

### [PowerShell](#tab/apponly-powershell)

```powershell
$credential = Get-Credential
Connect-PartnerCenter -Credential $credential -TenantId '<TenantId>'
```

> [!NOTE]  
> When you are prompted for credentials specify the client identifier as the username and the client secret as the password.

### [REST](#tab/apponly-rest)

Request:

```http
POST https://login.microsoftonline.com/{tenantId}/oauth2/token HTTP/1.1
Accept: application/json
return-client-request-id: true
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 194
Expect: 100-continue

resource=https%3A%2F%2Fgraph.windows.net&client_id={client-id-here}&client_secret={client-secret-here}&grant_type=client_credentials
```

Response:

```http
HTTP/1.1 200 OK
Cache-Control: no-cache, no-store
Pragma: no-cache
Content-Type: application/json; charset=utf-8
Expires: -1
Content-Length: 1406

{"token_type":"Bearer","expires_in":"3600","ext_expires_in":"3600","expires_on":"1546469802","not_before":"1546465902","resource":"https://graph.windows.net","access_token":"value-has-been-removed"}
```

---

## App + User authentication

Historically the [resource owner password credentials grant](https://tools.ietf.org/html/rfc6749#section-4.3) has been used to request an access token for use with the Partner Center REST API, .NET API, Java API, or PowerShell module. This is where you request an access token from Azure Active Directory using a client identifier and user credentials. This approach will no longer work because Partner Center requires multi-factor authentication, when using app + user authentication. To comply with this requirement Microsoft has introduced a secure, scalable framework for authenticating Cloud Solution Provider (CSP) partners and control panel vendors (CPV) using multi-factor authentication. This framework is known as the Secure Application Model, and it is comprised of a consent process and a request for an access token using a refresh token.

### Partner consent

The partner consent process is an interactive process where the partner authenticates using multi-factor authentication, consents to the application, and a refresh token is stored in a secure repository such as Azure Key Vault. We recommend that a dedicated account for integration purposes be used for this process.

> [!IMPORTANT]  
> The appropriate multi-factor authentication solution should be enabled for the service account used in the partner consent process. If it is not then the resulting refresh token will not be compliant with security requirements.

### Samples for App + User authentication

The partner consent process can be performed in a number of ways. To help partners understand how to perform each required operation, we have developed the following samples. Please note that these are samples only. When you implement the appropriate solution in your environment, it is important that you develop a solution that is complaint with your coding standards and security policies.

#### [.NET](#tab/appuser-dotnet)

The [partner consent](https://github.com/Microsoft/Partner-Center-DotNet-Samples/tree/master/secure-app-model/keyvault) sample project demonstrates how to utilize a website developed using ASP.NET to capture consent, request a refresh token, and securely store it in Azure Key Vault. Perform the following steps to create the required prerequisites for this sample.

1. Create an instance of Azure Key Vault using the Azure management portal or the following PowerShell commands. Before executing the command be sure to modify the parameter values accordingly. The vault name must be unique.

    ```azurepowershell-interactive
    Login-AzureRmAccount

    # Create a new resource group
    New-AzureRmResourceGroup -Name ContosoResourceGroup -Location EastUS

    New-AzureRmKeyVault -Name 'Contoso-Vault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
    ```

    If you need help creating the instance of Azure Key Vault see [Quickstart: Set and retrieve a secret from Azure Key Vault using the Azure portal](https://docs.microsoft.com/azure/key-vault/quick-create-portal) or [Quickstart: Set and retrieve a secret from Azure Key Vault using PowerShell](https://docs.microsoft.com/azure/key-vault/quick-create-powershell) for step by step directions on how to create an instance of Azure Key Vault, then set and retrieve a secret.

2. Create an Azure AD Application and a key using the Azure management portal or the following commands.

    ```azurepowershell-interactive
    Connect-AzureAD

    $SessionInfo = Get-AzureADCurrentSessionInfo

    $app = New-AzureADApplication -DisplayName 'My Vault Access App' -IdentifierUris 'https://$($SessionInfo.TenantDomain)/$((New-Guid).ToString())'
    $password = New-AzureADApplicationPasswordCredential -ObjectId $app.ObjectId

    Write-Host "ApplicationId       = $($app.AppId)"
    Write-Host "ApplicationSecret   = $($password.Value)"
    ```

    Be sure to make note of the application identifier and secret values because they will be used in the steps below.

3. Grant the newly create Azure AD application the read secrets permissions using the Azure management portal or the following commands.

    ```azurepowershell-interactive
    $app = Get-AzureADApplication -Filter {AppId -eq 'ENTER-APP-ID-HERE'}

    Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $app.ObjectId –PermissionsToSecrets get
    ```

4. Create an Azure AD application that is configured for Partner Center. Perform the following actions to complete this step.

    - Browse to the [App management](https://partner.microsoft.com/pcv/apiintegration/appmanagement) feature of the Partner Center Dashboard
    - Click *Add new web app* to create a new Azure AD application.

    Be sure to document the *App ID*, *Account ID**, and *Key* values because they will be used in the steps below.

5. Clone the [Partner-Center-DotNet-Samples](https://github.com/Microsoft/Partner-Center-DotNet-Samples) repository using Visual Studio or the following command.

    ```bash
    git clone https://github.com/Microsoft/Partner-Center-DotNet-Samples.git
    ```

6. Open the *PartnerConsent* project found in the `Partner-Center-DotNet-Samples\secure-app-model\keyvault` directory.
7. Populate the application settings found in the [web.config](https://github.com/Microsoft/Partner-Center-DotNet-Samples/blob/master/secure-app-model/keyvault/PartnerConsent/Web.config)

    ```xml
    <!-- AppID that represents CSP application -->
    <add key="ida:CSPApplicationId" value="" />
    <!-- 
        Please use certificate as your client secret and deploy the certificate to your environment.
        The following application secret is for sample application only. please do not use secret directly from the config file.    
    -->
    <add key="ida:CSPApplicationSecret" value="" />

    <!-- 
        Endpoint address for the instance of Azure KeyVault. This is
        the DNS Name for the instance of Key Vault that you provisioned.
     -->
    <add key="KeyVaultEndpoint" value="" />

    <!-- App ID that is given access for KeyVault to store refresh tokens -->
    <add key="ida:KeyVaultClientId" value="" />

    <!-- 
        Please use certificate as your client secret and deploy the certificate
        to your environment. The following application secret is for sample
        application only. please do not use secret directly from the config file.    
    -->
    <add key="ida:KeyVaultClientSecret" value="" />
    ```

    > [!IMPORTANT]  
    > Sensitive information such as application secrets should not be stored in configuration files. It was done here because this is a sample application. With your production application we strongly recommend that you use [certificate-based authentication](https://docs.microsoft.com/en-us/azure/key-vault/certificate-scenarios).

8. When you run this sample project it will prompt you for authentication. After successfully authenticating, an access token is requested from Azure AD. The information returned from Azure AD includes a refresh token which is stored in the configured instance of Azure Key Vault.  

#### [Java](#tab/appuser-java)

The [partner consent](https://github.com/Microsoft/Partner-Center-Java-Samples/tree/master/secure-app-model/keyvault) sample project demonstrates how to utilize a website developed using JSP to capture consent, request a refresh token, and secure store in Azure Key Vault. Perform the following to create the required prerequisites for this sample.

1. Create an instance of Azure Key Vault using the Azure management portal or the following PowerShell commands. Before executing the command be sure to modify the parameter values accordingly. The vault name must be unique.

    ```azurepowershell-interactive
    Login-AzureRmAccount

    # Create a new resource group
    New-AzureRmResourceGroup -Name ContosoResourceGroup -Location EastUS

    New-AzureRmKeyVault -Name 'Contoso-Vault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
    ```

    If you need help creating the instance of Azure Key Vault see [Quickstart: Set and retrieve a secret from Azure Key Vault using the Azure portal](https://docs.microsoft.com/azure/key-vault/quick-create-portal) or [Quickstart: Set and retrieve a secret from Azure Key Vault using PowerShell](https://docs.microsoft.com/azure/key-vault/quick-create-powershell) for step by step direction on how to create an instance of Azure Key Vault and set and retrieve a secret.

2. Create an Azure AD Application and a key using the Azure management portal or the following commands.

    ```azurepowershell-interactive
    Connect-AzureAD

    $SessionInfo = Get-AzureADCurrentSessionInfo

    $app = New-AzureADApplication -DisplayName 'My Vault Access App' -IdentifierUris 'https://$($SessionInfo.TenantDomain)/$((New-Guid).ToString())'
    $password = New-AzureADApplicationPasswordCredential -ObjectId $app.ObjectId

    Write-Host "ApplicationId       = $($app.AppId)"
    Write-Host "ApplicationSecret   = $($password.Value)"
    ```

    Be sure to document the application identifier and secret values because they will be used in the steps below.

3. Grant the newly created Azure AD application the read secrets permissions using the Azure management portal or the following commands.

    ```azurepowershell-interactive
    $app = Get-AzureADApplication -Filter {AppId -eq 'ENTER-APP-ID-HERE'}

    Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $app.ObjectId –PermissionsToSecrets get
    ```

4. Create an Azure AD application that is configured for Partner Center. Preform the following to complete this step.

    - Browse to the [App management](https://partner.microsoft.com/pcv/apiintegration/appmanagement) feature of the Partner Center Dashboard
    - Click *Add new web app* to create a new Azure AD application.

    Be sure to document the *App ID*, *Account ID**, and *Key* values because they will be used in the steps below.

5. Clone the [Partner-Center-Java-Samples](https://github.com/Microsoft/Partner-Center-Java-Samples) repository using the following command

    ```bash
    git clone https://github.com/Microsoft/Partner-Center-Java-Samples.git
    ```

6. Open the *PartnerConsent* project found in the `Partner-Center-Java-Samples\secure-app-model\keyvault` directory.
7. Populate the application settings found in the [web.xml](https://github.com/Microsoft/Partner-Center-Java-Samples/blob/master/secure-app-model/keyvault/partnerconsent/src/main/webapp/WEB-INF/web.xml) file

    ```xml
    <filter>
        <filter-name>AuthenticationFilter</filter-name>
        <filter-class>com.microsoft.store.samples.partnerconsent.security.AuthenticationFilter</filter-class>
        <init-param>
            <param-name>client_id</param-name>
            <param-value></param-value>
        </init-param>
        <init-param>
            <param-name>client_secret</param-name>
            <param-value></param-value>
        </init-param>
        <init-param>
            <param-name>keyvault_base_url</param-name>
            <param-value></param-value>
        </init-param>
        <init-param>
            <param-name>keyvault_client_id</param-name>
            <param-value></param-value>
        </init-param>
        <init-param>
            <param-name>keyvault_client_secret</param-name>
            <param-value></param-value>
        </init-param>
        <init-param>
            <param-name>keyvault_certifcate_path</param-name>
            <param-value></param-value>
        </init-param>
    </filter>
    ```

    > [!IMPORTANT]  
    > Sensitive information such as application secrets should not be stored in configurations files. It was done here because this is a sample application. With your production application, we strongly recommend that you use certificate based authenticate. See [Key Vault Certificate authentication](https://github.com/Azure-Samples/key-vault-java-certificate-authentication) for more information.

8. When you run this sample project it will prompt you for authentication. After successfully authenticating, an access token is requested from Azure AD. The information returned from Azure AD includes a refresh token which is stored in the configured instance of Azure Key Vault.  

#### [PowerShell](#tab/appuser-powershell)

Cloud Solution Provider partners can utilize the [Partner Center PowerShell](https://www.powershellgallery.com/packages/PartnerCenter) module to perform the partner consent process. This is made possible through the use of the [New-PartnerAccessToken](https://docs.microsoft.com/powershell/module/partnercenter/New-PartnerAccessToken) command. The following demonstrates how consent can be provided and the refresh token obtained.

```powershell
$credential = Get-Credential
$token = New-PartnerAccessToken -Consent -Credential $credential -Resource https://api.partnercenter.microsoft.com
```

When the `Get-Credential` command is invoked, you are prompted to enter a username and password. Specify the application identifier as the username and the application secret as the password. When then [New-PartnerAccessToken](https://docs.microsoft.com/powershell/module/partnercenter/new-partneraccesstoken) command is invoked you are again prompted for credentials. This time, you need to specify the credentials for the service account you are using. Please note that this should be a partner account with the appropriate permissions. After successful execution of the command, you'll find that the `$token` variable contains the response from Azure Active Directory for a token. Included in this response is a refresh token. Store this value in a secure repository such as Azure Key Vault or a similar service.

See [Partner Center PowerShell - Secure App Model](https://docs.microsoft.com/powershell/partnercenter/secure-app-model) for more information.

---

## Cloud Solution Provider authentication

Cloud Solution Provider partners can use the refresh token obtained through the [partner consent](#partner-consent) process.

### Samples for Cloud Solution Provider authentication

To help partners understand how to perform each required operation, we have developed the following samples. Please note that these are samples only. When you implement the appropriate solution in your environment, it is important that you develop a solution that is complaint with your coding standards and security policies.

#### [.NET](#tab/csp-dotnet)

1. If you have not already done so, perform the [partner consent process](#partner-consent).
2. Clone the [Partner-Center-DotNet-Samples](https://github.com/Microsoft/Partner-Center-DotNet-Samples) repository using Visual Studio or the following command

    ```bash
    git clone https://github.com/Microsoft/Partner-Center-DotNet-Samples.git
    ```

3. Open the `CSPApplication` project found in the `Partner-Center-DotNet-Samples\secure-app-model\keyvault` directory.
4. Update the application settings found in the [App.config](https://github.com/Microsoft/Partner-Center-DotNet-Samples/blob/master/secure-app-model/keyvault/CSPApplication/App.config) file.

    ```xml
    <!-- AppID that represents CSP application -->
    <add key="ida:CSPApplicationId" value="" />
    <!-- 
        Please use certificate as your client secret and deploy the certificate to your environment.
        The following application secret is for sample application only. please do not use secret directly from the config file.    
    -->
    <add key="ida:CSPApplicationSecret" value="" />

    <!-- Endpoint address for the instance of Azure KeyVault -->
    <add key="KeyVaultEndpoint" value="" />

    <!-- AppID that is given access for keyvault to store the refresh tokens -->
    <add key="ida:KeyVaultClientId" value="" />

    <!-- 
        Please use certificate as your client secret and deploy the certificate to your environment.
        The following application secret is for sample application only. please do not use secret directly from the config file.    
    -->
    <add key="ida:KeyVaultClientSecret" value="" />
    ```

5. Set the appropriate values for the **PartnerId** and **CustomerId** variables found in the [Program.cs](https://github.com/Microsoft/Partner-Center-DotNet-Samples/blob/master/secure-app-model/keyvault/CSPApplication/Program.cs) file.

    ```csharp
    // The following properties indicate which partner and customer context the calls are going to be made.
    string PartnerId = "<Partner tenant id>";
    string CustomerId = "<Customer tenant id>";
    ```

6. When you run this sample project it obtains the refresh token obtained during the partner consent process. Then, it requests an access token to interact with the Partner Center SDK on the partner's behalf. Finally, it requests an access token to interact with Microsoft Graph on behalf of the specified customer.

#### [Java](#tab/csp-java)

1. If you have not done so already, perform the [partner consent process](#partner-consent).
2. Clone the [Partner-Center-Java-Samples](https://github.com/Microsoft/Partner-Center-Java-Samples) repository using Visual Studio or the following command

    ```bash
    git clone https://github.com/Microsoft/Partner-Center-Java-Samples.git
    ```

3. Open the `cspsample` project found in the `Partner-Center-Java-Samples\secure-app-model\keyvault` directory.
4. Update the application settings found in the [application.properties](https://github.com/Microsoft/Partner-Center-Java-Samples/blob/master/secure-app-model/keyvault/cspsample/src/main/resources/application.properties) file.

     ```java
    azuread.authority=https://login.microsoftonline.com
    keyvault.baseurl=
    keyvault.clientId=
    keyvault.clientSecret=
    partnercenter.accountId=
    partnercenter.clientId=
    partnercenter.clientSecret=
    ```

5. When you run this sample project, it obtains the refresh token obtained during the partner consent process. Then, it requests an access token to interact with the Partner Center SDK on the partner's behalf.
6. Optional - un-comment the *RunAzureTask* and *RunGraphTask* function calls if you want to see how to interact with with Azure Resource Manager and Microsoft Graph on behalf of the customer.

#### [PowerShell](#tab/csp-powershell)

Connect to Partner Center using the [Connect-PartnerCenter](https://docs.microsoft.com/powershell/module/partnercenter/connect-partnercenter) command. You will need to retrieve the refresh token that was obtained during the [partner consent](#partner-consent) process, from the secure repository. Execute the following commands to request an access token and use it when connecting to Partner Center.

```powershell
$refreshToken = 'Enter the refresh token value here'

$credential = Get-Credential
$pcToken = New-PartnerAccessToken -RefreshToken $refreshToken -Resource https://api.partnercenter.microsoft.com -Credential $credential

Connect-PartnerCenter -AccessToken $pcToken.AccessToken -AccessTokenExpiresOn $pcToken.ExpiresOn -ApplicationId $appId
```

When the `Get-Credential` command is invoked, you are prompted to enter a username and password. Specify the application identifier as the username and the application secret as the password. When the [New-PartnerAccessToken](https://docs.microsoft.com/powershell/module/partnercenter/New-PartnerAccessToken) command is invoked, it requests a new access token using the specified refresh token from Azure Active Directory. That token is then used to connect to Partner Center.

See [Partner Center PowerShell - Secure App Model](https://docs.microsoft.com/powershell/partnercenter/secure-app-model) for more information.

---

## Control Panel Provider authentication

Control panel vendors need to have each partner they support perform the [partner consent](#partner-consent) process. Once that is completed the refresh token obtained through that process is used to access the Partner Center REST API and .NET API.

### Samples for Cloud Panel Provider authentication

To help control panel vendors understand how to perform each required operation, we have developed the following samples. Please note that these are samples only. When you implement the appropriate solution in your environment, it is important that you develop a solution that is complaint with your coding standards and security policies.

#### [.NET](#tab/cpp-dotnet)

1. Develop and deploy a process for Cloud Solution Provider partners to provide the appropriate consent. See the [partner consent](#partner-consent) for additional details and an example.

    > [!IMPORTANT]  
    > User credentials from a Cloud Solution Provider partner should not be stored. The refresh token obtained through the partner consent process should be stored and used to request access tokens for interacting with any Microsoft API.

2. Clone the [Partner-Center-DotNet-Samples](https://github.com/Microsoft/Partner-Center-DotNet-Samples) repository using Visual Studio or the following command

    ```bash
    git clone https://github.com/Microsoft/Partner-Center-DotNet-Samples.git
    ```

3. Open the `CPVApplication` project found in the `Partner-Center-DotNet-Samples\secure-app-model\keyvault` directory.
4. Update the application settings found in the [App.config](https://github.com/Microsoft/Partner-Center-DotNet-Samples/blob/master/secure-app-model/keyvault/CPVApplication/App.config) file.

    ```xml
    <!-- AppID that represents Control panel vendor application -->
    <add key="ida:CPVApplicationId" value="" />

    <!-- 
        Please use certificate as your client secret and deploy the certificate to your environment.
        The following application secret is for sample application only. please do not use secret directly from the config file.    
    -->
    <add key="ida:CPVApplicationSecret" value="" />

    <!-- Endpoint address for the instance of Azure KeyVault -->
    <add key="KeyVaultEndpoint" value="" />

    <!-- AppID that is given access for keyvault to store the refresh tokens -->
    <add key="ida:KeyVaultClientId" value="" />

    <!-- 
        Please use certificate as your client secret and deploy the certificate to your environment.
        The following application secret is for sample application only. please do not use secret directly from the config file.    
    -->
    <add key="ida:KeyVaultClientSecret" value="" />
    ```

5. Set the appropriate values for the **PartnerId** and **CustomerId** variables found in the [Program.cs](https://github.com/Microsoft/Partner-Center-DotNet-Samples/blob/master/secure-app-model/keyvault/CPVApplication/Program.cs) file.

    ```csharp
    // The following properties indicate which partner and customer context the calls are going to be made.
    string PartnerId = "<Partner tenant id>";
    string CustomerId = "<Customer tenant id>";
    ```

6. When you run this sample project it obtains the refresh token for the specified partner. Then, it requests an access token to access Partner Center and Azure AD Graph on behalf of the partner. The next task it performs is the deletion and creation of permission grants into the customer tenant. Since there is no relationship between the control panel vendor and the customer these permissions need to be added using the Partner Center API. The following example shows how this is accomplished.

    ```csharp
    JObject contents = new JObject
    {
        // Provide your application display name
        ["displayName"] = "CPV Marketplace",

        // Provide your application id
        ["applicationId"] = CPVApplicationId,

        // Provide your application grants
        ["applicationGrants"] = new JArray(
            JObject.Parse("{\"enterpriseApplicationId\": \"00000002-0000-0000-c000-000000000000\", \"scope\":\"Domain.ReadWrite.All,User.ReadWrite.All,Directory.Read.All\"}"), // for Azure AD Graph access,  Directory.Read.All
            JObject.Parse("{\"enterpriseApplicationId\": \"797f4846-ba00-4fd7-ba43-dac1f8f63013\", \"scope\":\"user_impersonation\"}")) // for Azure Resource Manager access
    };

    /**
     * The following steps have to be performed once per customer tenant if your application is
     * a control panel vendor application and requires customer tenant Azure AD Graph access.
     **/

    // delete the previous grant into customer tenant
    JObject consentDeletion = await ApiCalls.DeleteAsync(
        tokenPartnerResult.Item1,
        string.Format("https://api.partnercenter.microsoft.com/v1/customers/{0}/applicationconsents/{1}", CustomerId, CPVApplicationId));

    // create new grants for the application given the setting in application grants payload.
    JObject consentCreation = await ApiCalls.PostAsync(
        tokenPartnerResult.Item1,
        string.Format("https://api.partnercenter.microsoft.com/v1/customers/{0}/applicationconsents", CustomerId),
        contents.ToString());
    ```

After these permissions have been established, the sample performs operations using Azure AD Graph on behalf of the customer.

#### [Java](#tab/cpp-java)

1. Develop and deploy a process for Cloud Solution Provider partners to provide the appropriate consent. See the [partner consent](#partner-consent) for additional details and an example.

    > [!IMPORTANT]  
    > User credentials from a Cloud Solution Provider partner should not be stored. The refresh token obtained through the partner consent process should be stored and used to request access tokens for interacting with any Microsoft API.

2. Clone the [Partner-Center-Java-Samples](https://github.com/Microsoft/Partner-Center-Java-Samples) repository using the following command

    ```bash
    git clone https://github.com/Microsoft/Partner-Center-Java-Samples.git
    ```

3. Open the `cpvsample` project found in the `Partner-Center-Java-Samples\secure-app-model\keyvault` directory.
4. Update the application settings found in the [application.properties](https://github.com/Microsoft/Partner-Center-Java-Samples/blob/master/secure-app-model/keyvault/cpvsample/src/main/resources/application.properties) file.

    ```java
    azuread.authority=https://login.microsoftonline.com
    keyvault.baseurl=
    keyvault.clientId=
    keyvault.clientSecret=
    partnercenter.accountId=
    partnercenter.clientId=
    partnercenter.clientSecret=
    partnercenter.displayName=
    ```

    The value for the `partnercenter.displayName` should be the display name of your marketplace application.

5. Set the appropriate values for the **partnerId** and **customerId** variables found in the [Program.java](https://github.com/Microsoft/Partner-Center-Java-Samples/blob/master/secure-app-model/keyvault/cpvsample/src/main/java/com/microsoft/store/samples/secureappmodel/cpvsample/Program.java) file.

    ```java
    partnerId = "SPECIFY-THE-PARTNER-TENANT-ID-HERE";
    customerId = "SPECIFY-THE-CUSTOMER-TENANT-ID-HERE";
    ```

6. When you run this sample project it obtains the refresh token for the specified partner. Then, it requests an access token to access Partner Center on behalf of the partner. The next task it performs is the deletion and creation of permission grants into the customer tenant. Since there is no relationship between the control panel vendor and the customer these permissions need to be added using the Partner Center API. The following example shows how this is accomplished.

    ```java
    ApplicationGrant azureAppGrant = new ApplicationGrant();

    azureAppGrant.setEnterpriseApplication("797f4846-ba00-4fd7-ba43-dac1f8f63013");
    azureAppGrant.setScope("user_impersonation");

    ApplicationGrant graphAppGrant = new ApplicationGrant();

    graphAppGrant.setEnterpriseApplication("00000002-0000-0000-c000-000000000000");
    graphAppGrant.setScope("Domain.ReadWrite.All,User.ReadWrite.All,Directory.Read.All");

    ApplicationConsent consent = new ApplicationConsent();

    consent.setApplicationGrants(Arrays.asList(azureAppGrant, graphAppGrant));
    consent.setApplicationId(properties.getProperty(PropertyName.PARTNER_CENTER_CLIENT_ID));
    consent.setDisplayName(properties.getProperty(PropertyName.PARTNER_CENTER_DISPLAY_NAME));

    // Deletes the existing grant into the customer it is present.
    partnerOperations.getServiceClient().delete(
        partnerOperations,
        new TypeReference<ApplicationConsent>(){},
        MessageFormat.format(
            "customers/{0}/applicationconsents/{1}",
            customerId,
            properties.getProperty(PropertyName.PARTNER_CENTER_CLIENT_ID)));

    // Consent to the defined applications and the respective scopes.
    partnerOperations.getServiceClient().post(
        partnerOperations,
        new TypeReference<ApplicationConsent>(){},
        MessageFormat.format(
            "customers/{0}/applicationconsents",
            customerId),
        consent);
    ```

Un-comment the *RunAzureTask* and *RunGraphTask* function calls if you want to see how to interact with with Azure Resource Manager and Microsoft Graph on behalf of the customer.

---

## Frequently Asked Questions

### Can the trusted location conditional access policy be used to bypass the requirement for multi-factor authentication?

No, this will not work because of how the requirement for multi-factor authentication will be enforced.

### How will the requirement for multi-factor authentication be enforced?

This requirement will be enforced by ensuring that a claim of type <http://schemas.microsoft.com/claims/authnmethodsreferences> with a value of mfa is present. If it is not then authentication will be denied.
