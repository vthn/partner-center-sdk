---
title: Get prices for Microsoft Azure
description: How to get an Azure Rate Card with real-time prices for an Azure offer. Azure pricing is quite dynamic and changes frequently.
ms.assetid: 65262585-0F3B-4BD0-83BE-B2695C33CDB7
ms.date: 03/20/2019
ms.localizationpriority: medium
---

# Get prices for Microsoft Azure

**Applies To**

- Partner Center
- Partner Center for Microsoft Cloud Germany
- Partner Center for Microsoft Cloud for US Government

How to get an [Azure Rate Card](azure-rate-card-resources.md) with real-time prices for an Azure offer. Azure pricing is quite dynamic and changes frequently.

To track usage and help predict your monthly bill and the bills for individual customers, you can combine this Azure Rate Card query to get prices for Microsoft Azure with a request to [Get a customer's utilization records for Azure](get-a-customer-s-utilization-record-for-azure.md).

Prices differ by market and currency, and this API takes location into consideration. You can customize the currency, region and language returned in the response. This is especially relevant if you manage sales in multiple markets from a single, centralized office. See [URI parameters](#uri-parameters) for more information. 

## Examples

### C#

To obtain the Azure Rate Card, call the [**IAzureRateCard.Get**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.ratecards.iazureratecard.get) method to return an [**AzureRateCard**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.models.ratecards.azureratecard) resource that contains the Azure prices.

```csharp
// IAggregatePartner partnerOperations;

var azureRateCard = partner.RateCards.Azure.Get();
```

**Sample**: [Console test app](console-test-app.md). **Project**: Partner Center SDK Samples **Class**: GetAzureRateCard.cs

### Java

To obtain the Azure Rate Card, call the **IAzureRateCard.get** function to return rate card details that contains the Azure prices.

```java
// IAggregatePartner partnerOperations;

AzureRateCard azureRateCard = partner.getRateCards().getAzure().get();
```

### PowerShell

To obtain the Azure Card, execute the [**Get-PartnerAzureRateCard**](https://github.com/Microsoft/Partner-Center-PowerShell/blob/master/docs/help/Get-PartnerAzureRateCard.md) command to return rate card details that contains the Azure prices.

```powershell
Get-PartnerAzureRateCard
```

## Request

### Request syntax

| Method  | Request URI                                                        |
|---------|--------------------------------------------------------------------|
| **GET** | *{baseURL}*/v1/ratecards/azure?currency={currency}&region={region} |

### URI parameters

| Name     | Type   | Required | Description                                                                                                                                                                               |
|----------|--------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| currency | string | No       | Optional three letter ISO code for the currency in which the resource rates will be provided (e.g. "EUR"). The default is "USD". |
| region   | string | No       | Optional two-letter ISO country/region code that indicates the market where the offer is purchased (e.g. "FR"). The default is "US".        |

You can include the optional X-Locale [header](headers.md#request-headers) in your request. If you don't include the X-Locale header, the default value ("en-US") is used.
* If you provide currency and region parameters in your request, the value of X-Locale is used to determine the response's language.
* If you don't provide region and currency parameters in your request, the value of X-Locale is used to determine the response's region, currency and language.


### Request header

See [Partner Center REST headers](headers.md) for more information.

### Request body

None.

### Request example

```http
GET https://api.partnercenter.microsoft.com/v1/ratecards/azure HTTP/1.1
Authorization: Bearer <token>
Accept: application/json
MS-RequestId: 07ced227-3f32-4eeb-8062-f0bef849a9bc
MS-CorrelationId: a687bc47-8d08-4b78-aff6-5a59aa2055c2
X-Locale: en-US
Host: api.partnercenter.microsoft.com
Connection: Keep-Alive
```

## Response


If this is successful, it returns an [Azure Rate Card](azure-rate-card-resources.md) resource.

### Response success and error codes

Each response comes with an HTTP status code that indicates success or failure and additional debugging information. Use a network trace tool to read this code, error type, and additional parameters. For the full list, see [Partner Center REST error codes](error-codes.md).

### Response example

```http
HTTP/1.1 200 OK
Content-Length: 1545508
Content-Type: application/json; charset=utf-8
MS-CorrelationId: 57b25659-fc00-4215-87e7-2b09bac6845d
MS-RequestId: 870118d0-adbb-41a3-82d2-a3d45ade3c73
MS-CV: CYBB8PXMsEukJBIn.0
MS-ServerId: 201021413
Date: Wed, 01 Feb 2017 00:13:45 GMT

{
    "locale": "en-US",
    "currency": "USD",
    "isTaxIncluded": false,
    "meters": [{
            "id": "4b836326-7e19-46e6-8bce-1b19bb6cd91e",
            "name": "Unlimited Data - 1 Gbps",
            "rates": {
                "0": 7395.0
            },
            "tags": [],
            "category": "Networking",
            "subcategory": "ExpressRoute",
            "region": "Zone 2",
            "unit": "Connections",
            "includedQuantity": 0.0,
            "effectiveDate": "2015-09-01T00:00:00Z"
        }, {
            "id": "1e8f6d9f-8b40-4c97-80cc-cff87a290a93",
            "name": "Compute Hours",
            "rates": {
                "0": 3.9729
            },
            "tags": [],
            "category": "Cloud Services",
            "subcategory": "Standard_L16 Cloud Services",
            "region": "AU East",
            "unit": "1 Hour",
            "includedQuantity": 0.0,
            "effectiveDate": "2016-09-01T00:00:00Z"
        }, {
            "id": "7a2639ce-ae47-4413-9837-6b4f4b78be3d",
            "name": "Compute Hours",
            "rates": {
                "0": 0.1122
            },
            "tags": [],
            "category": "Virtual Machines",
            "subcategory": "Standard_D1_v2 VM (Windows)",
            "region": "BR South",
            "unit": "Hours",
            "includedQuantity": 0.0,
            "effectiveDate": "2017-01-01T00:00:00Z"
        }
    ],
    "offerTerms": [{
            "name": "Overage discount",
            "discount": 0.15,
            "excludedMeterIds": ["53cc0061-0fe2-4249-bf62-e1008c811f5c", "c82dbd27-c978-43a7-ad41-525a90d8962b"],
            "effectiveDate": "2014-01-01T00:00:00"
        }
    ],
    "attributes": {
        "objectType": "AzureRateCard"
    }
}
```