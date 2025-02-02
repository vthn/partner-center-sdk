---
title: Get a customer's qualification
description: How to get a customer's qualification.
ms.date: 08/07/2019
ms.localizationpriority: medium
---


# Get a customer's qualification

**Applies To**

- Partner Center

How to get a customer's qualification.


## <span id="Prerequisites"/><span id="prerequisites"/><span id="PREREQUISITES"/>Prerequisites

- Credentials as described in [Partner Center authentication](partner-center-authentication.md). This scenario supports authentication with both standalone App and App+User credentials.
- A customer identifier.


## <span id="C_"/><span id="c_"/>C#

To get a customer's qualification, call the [**IAggregatePartner.Customers.ById**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.customers.icustomercollection.byid) method with the customer identifier. Then use the [**Qualification**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.customers.icustomer.qualification) property to retrieve a [**ICustomerQualification**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.qualification.icustomerqualification) interface. Finally, call [**Get**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.subscriptions.isubscriptioncollection.get) or [**GetAsync**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.subscriptions.isubscriptioncollection.getasync) to retrieve the customer's qualification.

``` csharp
// IAggregatePartner partnerOperations;
// string customerId;

var customerQualification = partnerOperations.Customers.ById(customerId).Qualification.Get();
```


## <span id="Request"/><span id="request"/><span id="REQUEST"/>Request

**Request syntax**

| Method  | Request URI                                                                                          |
|---------|------------------------------------------------------------------------------------------------------|
| **GET** | [*{baseURL}*](partner-center-rest-urls.md)/v1/customers/{customer-tenant-id}/qualification HTTP/1.1 |

 
**URI parameter**

This table lists the required query parameter to get all the qualification.

| Name               | Type   | Required | Description                                           |
|--------------------|--------|----------|-------------------------------------------------------|
| **customer-tenant-id** | string | Yes      | A GUID-formatted string that identifies the customer. |

**Request headers**

- See [Partner Center REST headers](headers.md) for more information.

**Request body**

None.

**Request example**

```http
GET https://api.partnercenter.microsoft.com/v1/customers/<customer-tenant-id>/qualification HTTP/1.1
Authorization: Bearer <token>
Accept: application/json
MS-CorrelationId: 7d2456fd-2d79-46d0-9f8e-5d7ecd5f8745
MS-RequestId: 037db222-6d8e-4d7f-ba78-df3dca33fb68
```


## <span id="Response"/><span id="response"/><span id="RESPONSE"/>Response

If successful, this method returns a qualification value in the response body.  Below is an example of the **GET** call on a customer with the **education** qualification.

**Response success and error codes**

Each response comes with an HTTP status code that indicates success or failure and additional debugging information. Use a network trace tool to read this code, error type, and additional parameters. For the full list, see [Partner Center REST error codes](error-codes.md).

**Response example**

```http
HTTP/1.1 200 OK
Content-Length: 
Content-Type: application/json
MS-CorrelationId: 7d2456fd-2d79-46d0-9f8e-5d7ecd5f8745
MS-RequestId: 037db222-6d8e-4d7f-ba78-df3dca33fb68

    "education"

```

## Related topics
 - [Update a customer's qualification](update-a-customer-s-qualification.md)

