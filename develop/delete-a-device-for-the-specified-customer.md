---
title: Delete a device for the specified customer
description: How to delete a device that belongs to a specified customer.
ms.assetid: 44F06D4B-E9DE-470F-BAE2-15205CC7C699
ms.date: 06/20/2019
ms.localizationpriority: medium
---

# Delete a device for the specified customer

Applies to:

- Partner Center
- Partner Center for Microsoft Cloud Germany

This topic explains how to delete a device that belongs to a specified customer.

## Prerequisites

- Credentials as described in [Partner Center authentication](partner-center-authentication.md). This scenario supports authentication with both standalone App and App+User credentials.
- The customer identifier.
- The device batch identifier.
- The device identifier.

## C\#

To delete a device for the specified customer:

1. Call the [**IAggregatePartner.Customers.ById**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.customers.icustomercollection.byid) method with the customer identifier to retrieve an interface to operations on the customer.
2. Call the [**DeviceBatches.ById**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.devicesdeployment.idevicesbatchcollection.byid) method with the device batch identifier to get an interface to operations for the specified batch.
3. Call the [**Devices.ById**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.devicesdeployment.idevicecollection.byid) method to get an interface to operation on the specified device.
4. Call the [**Delete**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.devicesdeployment.idevice.delete) or [**DeleteAsync**](https://docs.microsoft.com/dotnet/api/microsoft.store.partnercenter.devicesdeployment.idevice.deleteasync) method to delete the device from the batch.

``` csharp
IAggregatePartner partnerOperations;
string selectedCustomerId;
string selectedDeviceBatchId;
string selectedDeviceId;

partnerOperations.Customers.ById(selectedCustomerId).DeviceBatches.ById(selectedDeviceBatchId).Devices.ById(selectedDeviceId).Delete();
```

**Sample**: [Console test app](console-test-app.md). **Project**: Partner Center SDK Samples **Class**: DeleteDevice.cs

## REST request

### Request syntax

| Method     | Request URI                                                                                                                        |
|------------|------------------------------------------------------------------------------------------------------------------------------------|
| DELETE     | [*{baseURL}*](partner-center-rest-urls.md)/v1/customers/{customer-id}/deviceBatches/{devicebatch-id}/devices/{device-id} HTTP/1.1  |

#### URI parameters

Use the following path parameters when creating the request.

| Name           | Type   | Required | Description                                                        |
|----------------|--------|----------|--------------------------------------------------------------------|
| customer-id    | string | Yes      | A GUID-formatted string that identifies the customer.              |
| devicebatch-id | string | Yes      | The device batch identifier of the batch that contains the device. |
| device-id      | string | Yes      | The device identifier.                                             |

### Request headers

See [Partner Center REST headers](headers.md) for more information.

### Request body

None

### Request example

```http
DELETE https://api.partnercenter.microsoft.com/v1/customers/47021739-3426-40bf-9601-61b4b6d7c793/deviceBatches/testbatch/devices/7b11cd8b-dd1e-4840-8c4a-84215e4de782 HTTP/1.1
Authorization: Bearer <token>
MS-RequestId: e88d014d-ab70-41de-90a0-f7fd1797267d
MS-CorrelationId: de894e18-f027-4ac0-8b5a-34f0c222af0c
X-Locale: en-US
Content-Length: 0
Content-Type: application/json
Host: api.partnercenter.microsoft.com
```

## REST response

If successful, the response returns a **204 No Content** status code.

### Response success and error codes

Each response comes with an HTTP status code that indicates success or failure and additional debugging information. Use a network trace tool to read this code, error type, and additional parameters. For the full list, see [Partner Center REST error codes](error-codes.md).

### Response example

```http
HTTP/1.1 204 No Content
Content-Length: 0
MS-CorrelationId: 394d96d0-05b2-4b02-b907-0697632ee3bb
MS-RequestId: 8b3e6f78-220b-4177-861b-33d6f38f7b97
MS-CV: YrLe3w6BbUSMt1fi.0
MS-ServerId: 030020344
Date: Tue, 25 Jul 2017 17:58:53 GMT
```
