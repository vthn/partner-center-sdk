---
title: Get service request support topics
description: Gets a collection of items representing valid topics for service requests.
ms.assetid: 50A61342-70C4-49F5-BEA2-2754338CF5A1
ms.date: 12/15/2017
ms.localizationpriority: medium
---

# Get service request support topics

**Applies To**

- Partner Center
- Partner Center for Microsoft Cloud Germany
- Partner Center for Microsoft Cloud for US Government

Gets a collection of items representing valid topics for service requests.

## <span id="Prerequisites"/><span id="prerequisites"/><span id="PREREQUISITES"/>Prerequisites


- Credentials as described in [Partner Center authentication](partner-center-authentication.md). This scenario supports authentication with App+User credentials only.

## <span id="C_"/><span id="c_"/>C#


To get a collection of service request topics, use your [**IPartnerOperations**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.ipartner) collection to retrieve the [**ServiceRequests**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.ipartner.servicerequests) property of the resulting object, followed by the [**SupportTopics**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.servicerequests.isupporttopicscollection) property and the [**Get()**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.servicerequests.isupporttopicscollection.get) or [**GetAsync()**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.servicerequests.isupporttopicscollection.getasync) methods.

``` csharp
// IPartner partnerOperations;

ResourceCollection<SupportTopic> supportTopicsCollection = partnerOperations.ServiceRequests.SupportTopics.Get();
```

## <span id="REST_Request"/><span id="rest_request"/><span id="REST_REQUEST"/>REST Request


**Request syntax**

| Method  | Request URI                                                                           |
|---------|---------------------------------------------------------------------------------------|
| **GET** | [*{baseURL}*](partner-center-rest-urls.md)/v1/servicerequests/supporttopics HTTP/1.1 |

 

**Request headers**

- See [Headers](headers.md) for more information.

**Request body**

None.

**Request example**

```http
GET https://api.partnercenter.microsoft.com/v1/servicerequests/supporttopics HTTP/1.1
Authorization: Bearer <token>
Accept: application/json
MS-RequestId: 4c1e8b6c-d136-4931-9df9-d85ec08ccffe
MS-CorrelationId: f447b215-f9bc-48da-a05a-3b5322d86a9c
X-Locale: en-US
```

## <span id="Response"/><span id="response"/><span id="RESPONSE"/>Response


If successful, this method returns a collection of the valid topics for a support request.

**Response success and error codes**

Each response comes with an HTTP status code that indicates success or failure and additional debugging information. Use a network trace tool to read this code, error type, and additional parameters. For the full list, see [Error Codes](error-codes.md).

**Response example**

```http
HTTP/1.1 200 OK
Content-Length: 4982
Content-Type: application/json
MS-CorrelationId: f447b215-f9bc-48da-a05a-3b5322d86a9c
MS-RequestId: 4c1e8b6c-d136-4931-9df9-d85ec08ccffe
Date: Fri, 29 Jan 2016 22:31:36 GMT

{
    "totalCount":14,
    "items":[{
        "name":"Partner Center Issues",
        "description":"Office 365 questions from the CSP (Cloud Solution Provider) partners using Partner Center",
        "id":32444667,
        "attributes":{
            "objectType":"SupportTopic"
        }
    },
    {
        "name":"Cannot manage my profile",
        "description":"Issues that are related to errors or problems with managing a profile",
        "id":32444671,
        "attributes":{
            "objectType":"SupportTopic"
        }
    }],
    "attributes":{
        "objectType":"Collection"
    }
}
```