---
title: Get a list of add-ons for a subscription
description: How to get a collection of add-ons that a customer has chosen to add to their subscription.
ms.assetid: F10823A2-CA77-4F80-9ED4-CFB4E4343049
ms.date: 07/25/2019
ms.localizationpriority: medium
---

# Get a list of add-ons for a subscription

Applies to:

- Partner Center
- Partner Center operated by 21Vianet
- Partner Center for Microsoft Cloud Germany
- Partner Center for Microsoft Cloud for US Government

This topic describes how to get a collection of add-ons that a customer has chosen to add to their **[Subscription](subscription-resources.md)** resource.

## Prerequisites

- Credentials as described in [Partner Center authentication](partner-center-authentication.md). This scenario supports authentication with both standalone App and App+User credentials.
- A customer ID (**customer-tenant-id**). If you do not have a customer's ID, you can look up the ID in Partner Center by choosing the customer from the customers list, selecting Account, then saving their Microsoft ID.
- A subscription ID.

## C\#

To get the list of add-ons for a customer's subscription:

1. Use your **IAggregatePartner.Customers** collection to call the **ById()** method.
2. Call the [**Subscriptions**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.customers.icustomer.subscriptions) property, followed by the [**ById()**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.subscriptions.isubscriptioncollection.byid) method.
3. Call the [**Addons**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.subscriptions.isubscription.addons) property, followed by [**Get()**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.subscriptions.isubscriptionaddoncollection.get) or [**GetAsync()**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.subscriptions.isubscriptionaddoncollection.getasync).

``` csharp
// IAggregatePartner partnerOperations;
// var selectedCustomerId as string;
// var selectedSubscription Subscription;

var subscriptionDetails = partnerOperations.Customers.ById(selectedCustomerId).Subscriptions.ById(selectedSubscription.Id).AddOns.Get();


```

For an example, see the following:

- Sample: [Console test app](console-test-app.md)
- Project: **PartnerSDK.FeatureSample**
- Class: **SubscriptionAddons.cs**

## REST request

### Request syntax

| Method  | Request URI                                                                                                                       |
|---------|-----------------------------------------------------------------------------------------------------------------------------------|
| **GET** | [*{baseURL}*](partner-center-rest-urls.md)/v1/customers/{customer-tenant-id}/subscriptions/{id-for-subscription}/addons HTTP/1.1 |

#### URI parameter

This table lists the required query parameters to get the list of add-ons for the subscription.

| Name                    | Type     | Required | Description                               |
|-------------------------|----------|----------|-------------------------------------------|
| **customer-tenant-id**  | **guid** | Y        | A GUID corresponding to the customer.     |
| **id-for-subscription** | **guid** | Y        | A GUID corresponding to the subscription. |

### Request headers

See [Headers](headers.md) for more information.

### Request body

None.

### Request example

```http
GET https://api.partnercenter.microsoft.com/v1/customers/<customer-tenant-id>/subscriptions/<id-for-subscription>/addons HTTP/1.1
Authorization: Bearer <token>
Accept: application/json
MS-RequestId: 429902e2-ea2f-4704-b8a0-27fc53c539ba
MS-CorrelationId: c49004b1-224f-4d86-a607-6c8bcc52cfdd
```

## REST response

If successful, this method returns a collection of resources in the response body.

### Response success and error codes

Each response comes with an HTTP status code that indicates success or failure and additional debugging information. Use a network trace tool to read this code, error type, and additional parameters. For a full list, see [Error Codes](error-codes.md).

### Response example

```http
HTTP/1.1 200 OK
Content-Length: 73754
Content-Type: application/json
MS-CorrelationId: c49004b1-224f-4d86-a607-6c8bcc52cfdd
MS-RequestId: 16fee928-dc2c-412f-adbb-871f68babf16
Date: Wed, 25 Nov 2015 05:50:45 GMT

{
    "totalCount": 37,
    "items": [{
        "id": "83ef9d05-4169-4ef9-9657-0e86b1eab1de",
        "entitlementId": "42226ed6-070a-4e0f-b80c-4cdfB3e97aa7",
        "friendlyName": "Myofferpurchase",
        "quantity": 1,
        "unitType": "none",
        "creationDate": "2015-11-25T06: 41: 12Z",
        "effectiveStartDate": "2015-11-24T08: 00: 00Z",
        "commitmentEndDate": "2016-12-12T08: 00: 00Z",
        "status": "active",
        "autoRenewEnabled": false,
        "billingType": "none",
        "contractType": "subscription",
        "links": {
            "offer": {
                "uri": "/v1/offers/0CCA44D6-68E9-4762-94EE-31ECE98783B9",
                "method": "GET",
                "headers": []
            },
            "self": {
                "uri": "/subscriptions?key=<key>",
                "method": "GET",
                "headers": []
            }
        },
        "orderId": "6183db3d-6318-4e52-877e-25806e4971be",
        "attributes": {
            "etag": "<etag>",
            "objectType": "Subscription"
        }
    }],
    "attributes": {
        "objectType": "Collection"
    }
}
```
