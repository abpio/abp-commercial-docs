## PayPaymentRequests

PayPaymentRequests Table is used to store payment requests.

### Description

This table stores information about the payment requests. Each record in the table represents a payment request and allows to manage and track the payment requests effectively. This table is important for managing and tracking the payment requests of the application.

### Module

[`Volo.Payment`](../payment.md)

### Used By

| Table | Column | Description |
| --- | --- | --- |
| [`PayPaymentRequestProducts`](#paypaymentrequestproducts) | PaymentRequestId | To match the payment request product with the payment request. |

---

## PayPaymentRequestProducts

PayPaymentRequestProducts Table is used to store payment request products.

### Description

This table stores information about the payment request products. Each record in the table represents a payment request product and allows to manage and track the payment request products effectively. This table is important for managing and tracking the payment request products of the application.

### Module

[`Volo.Payment`](../payment.md)

### Uses

| Table | Column | Description |
| --- | --- | --- |
| [`PayPaymentRequests`](#paypaymentrequests) | Id | To match the payment request product with the payment request. |

---

## PayPlans

PayPlans Table is used to store payment plans.

### Description

This table stores information about the payment plans. Each record in the table represents a payment plan and allows to manage and track the payment plans effectively. This table is important for managing and tracking the payment plans of the application.

### Module

[`Volo.Payment`](../payment.md)

### Used By

| Table | Column | Description |
| --- | --- | --- |
| [`PayGatewayPlans`](#paygatewayplans) | PlanId | To match the payment gateway plan with the plan. |

---

## PayGatewayPlans

PayGatewayPlans Table is used to store payment gateway plans.

### Description

This table stores information about the payment gateway plans. Each record in the table represents a payment gateway plan and allows to manage and track the payment gateway plans effectively. This table is important for managing and tracking the payment gateway plans of the application.

### Module

[`Volo.Payment`](../payment.md)

### Uses

| Table | Column | Description |
| --- | --- | --- |
| [`PayPlans`](#payplans) | Id | To match the payment gateway plan with the plan. |