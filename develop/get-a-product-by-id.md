---
title: Get a product by ID
description: Gets the specified product resource using a product ID.
ms.assetid: 5E4160AB-6B73-4CA1-903D-7257927CA754
ms.date: 01/08/2019
ms.localizationpriority: medium
---

# Get a product by ID

**Applies To**

- Partner Center

Gets the specified product resource using a product ID.

## <span id="Prerequisites"/><span id="prerequisites"/><span id="PREREQUISITES"/>Prerequisites

- Credentials as described in [Partner Center authentication](partner-center-authentication.md). This scenario supports authentication with both standalone App and App+User credentials.
- A product ID.

## <span id="Examples"/><span id="examples"><span id="EXAMPLES"/>Examples

### C#

To find a specific product by ID, use your **IAggregatePartner.Products** collection, select the country by using the **ByCountry()** method, then call the **ById()** method. Finally, call the **Get()** or **GetAsync()** method to return the product. 

```csharp
// IAggregatePartner partnerOperations;

Product productDetail = partnerOperations.Products.ByCountry("US").ById("DZH318Z0BQ3Q").Get();
```

### Java

To find a specific product by ID, use your **IAggregatePartner.getProducts** function, select the country by using the **byCountry()** function, then call the **byId()** function. Finally, call the **get()** function to return the product. 

```java
// IAggregatePartner partnerOperations;

Product productDetail = partnerOperations.getProducts().byCountry("US").byId("DZH318Z0BQ3Q").get();
```

### PowerShell

To find a specific product by ID, execute the [**Get-PartnerProduct**](https://github.com/Microsoft/Partner-Center-PowerShell/blob/master/docs/help/Get-PartnerProduct.md) command and specify the **ProductId** paramater. The **CountryCode** paramater is options, if it is not specified then the country associated with the reseller will be used.

```powershell
Get-PartnerProduct -ProductId 'DZH318Z0BQ3Q'
```

## <span id="REST_Request"/><span id="rest_request"/><span id="REST_REQUEST"/>REST Request

**Request syntax**

| Method  | Request URI                                                                                   |
|---------|-----------------------------------------------------------------------------------------------|
| **GET** | [*{baseURL}*](partner-center-rest-urls.md)/v1/products/{product-id}?country={country} HTTP/1.1  | 

**URI parameter**

Use the following path parameters to get the specified product.

| Name                   | Type     | Required | Description                                                     |
|------------------------|----------|----------|-----------------------------------------------------------------|
| product-id             | string   | Yes      | A string that identifies the product.                           |
| country                | string   | Yes      | A country/region ID.                                            |


**Request headers**

- See [Headers](headers.md) for more information.

**Request body**

None.

**Request example**

```http
GET https://api.partnercenter.microsoft.com/v1/products/{product-id}?country=US HTTP/1.1
Authorization: Bearer 
Accept: application/json
MS-RequestId: 031160b2-b0b0-4d40-b2b1-aaa9bb84211d
MS-CorrelationId: 7c1f6619-c176-4040-a88f-2c71f3ba4533
```

## <span id="Response"/><span id="response"/><span id="RESPONSE"/>Response


If successful, the response body contains a [Product](product-resources.md#product) resource.

**Response success and error codes**

Each response comes with an HTTP status code that indicates success or failure and additional debugging information. Use a network trace tool to read this code, error type, and additional parameters. For the full list, see [Partner Center error codes](error-codes.md).

This method returns the following error codes:

| HTTP Status Code     | Error code   | Description                                                                |
|----------------------|--------------|----------------------------------------------------------------------------|
| 404                  | 400013       | Product was not found.                                                     |

**Response example**

```http
HTTP/1.1 200 OK
Content-Length: 1918
Content-Type: application/json
MS-CorrelationId: 7c1f6619-c176-4040-a88f-2c71f3ba4533
MS-RequestId: ac943950-ba3d-47a0-bd2a-c5617a7fefe8
Date: Tue, 23 Jan 2018 23:13:01 GMT

{
    "id": "DZH318Z0BQ3Q",
    "title": "Virtual Machines DSv2 Series",
    "description": "Dsv2-series instances are the latest generation of D-series instances that will carry more powerful CPUs which are on average about 35% faster than D-series instances, and carry the same memory and disk configurations as the D-series. Dsv2-series instances are based on the latest generation 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) processor, and with Intel Turbo Boost Technology 2.0 can go to 3.2 GHz.",
    "productType": {
        "id": "Azure",
        "displayName": "Azure",
        "subType": {
			"id": "VirtualMachines",
			"displayName": "VirtualMachines"
        }
    },
    "isMicrosoftProduct": true,
    "publisherName": "Microsoft",
    "links": {
        "skus": {
            "uri": "/products/DZH318Z0BQ3Q/skus?country=US",
            "method": "GET",
            "headers": []
        },
        "self": {
            "uri": "/products/DZH318Z0BQ3Q?country=US",
            "method": "GET",
            "headers": []
        }
    }
}
```