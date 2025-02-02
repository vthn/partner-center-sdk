---
title: Agreement resources
description: The Agreement resource represents a Microsoft cloud customer agreement.
ms.date: 08/28/2019
ms.localizationpriority: medium
---

# Agreement resources

Applies to:

- Partner Center

The **Agreement** resource is currently supported by Partner Center in the Microsoft public cloud only. It is not applicable to:

- Partner Center operated by 21Vianet
- Partner Center for Microsoft Cloud Germany
- Partner Center for Microsoft Cloud for US Government

The **Agreement** resource represents a Microsoft cloud customer agreement.

## Agreement

The **Agreement** resource represents the details of certification provided by the partner.

| Property       | Type   | Description                                                                                               |
|----------------|--------|-----------------------------------------------------------------------------------------------------------|
| userId         | string                         | Object identifier of the logged-in user in the partner tenant who is providing confirmation on behalf of the partner organization. When using App+User authentication to create an Agreement resource, Partner Center automatically derives the **userId** attribute value from the App+User token.                                                                             |
| primaryContact | [Contact](./utility-resources.md#contact) | Information about the user from the customer organization that accepted the Microsoft Cloud Agreement, including:  **firstName**, **lastName**, **email**, and **phoneNumber** (optional). |
| dateAgreed     | string in UTC date time format | The date when the customer accepted the agreement.                                 |
| templateId     |string                          | Unique identifier of the agreement that the customer accepted. Currently, the only supported value is **998b88de-aa99-4388-a42c-1b3517d49490**, which is the unique identifier for the Microsoft Cloud Agreement.                             |
| type           |string                          | Agreement type. Currently, supported values include **MicrosoftCloudAgreement** and **MicrosoftCustomerAgreement** (preview).|
| agreementLink  | string                         | URL for the agreement template.                                                    |
