# Payment module

This module implements payment gateway integration of an application;

* Supports [Stripe](https://stripe.com/), [PayPal](https://www.paypal.com/), [2Checkout](https://www.2checkout.com/), [PayU](https://corporate.payu.com/) and [Iyzico](https://www.iyzico.com/en) payment gateways.

See [the module description page](https://commercial.abp.io/modules/Volo.Payment) for an overview of the module features.

You can get payment from your customers using one or more payment gateways supported by the payment module. Payment module works in a very simple way. It creates a local payment request record and redirects customer to payment gateway (PayPal, Stripe etc...) for the payment. When the customer pays on the payment gateway, payment module handles the external payment gateway's response and validates the payment to see if it is really paid or not. If the payment is validated, payment module redirects customer to mail application who initiated the payment process at the beginning.

Image below demonstrates the flow of a payment process;

![payment-module-flow](../images/payment-module-flow.png)

Each payment gateway implementation contains PrePayment and PostPayment pages. 

PrePayment page asks users for extra information if requested by the external payment gateway. For example, 2Checkout doesn't require any extra information, so PrePayment page for 2Checkout redirects user to 2Checkout without asking any extra information. 

PostPayment page is responsible for validation the response of the external payment gateway. When a user completes the payment, user is redirected to PostPayment page for that payment gateway and PostPayment page validates the status of the payment. If the payment is succeeded, status of the payment request is updated and user is redirected to main application.

## How to install

Payment module is not installed in [the startup templates](../startup-templates/application/index.md). So, it needs to be installed manually. There are two ways of installing a module into your application.


### 1. Using ABP CLI

ABP CLI allows adding a module to a solution using ```add-module``` command. You can check its [documentation](https://docs.abp.io/en/abp/latest/CLI#add-module) for more information. So, payment module can be added using the command below;

```bash
abp add-module Volo.Payment
```

### 2. Manual Installation

If you modified your solution structure, adding module using ABP CLI might not work for you. In such cases,  payment module can be added to a solution manually.

In order to do that, add packages listed below to matching project on your solution. For example, ```Volo.Payment.Application``` package to your **{ProjectName}.Application.csproj** like below;

```json
<PackageReference Include="Volo.Payment.Application" Version="x.x.x" />
```

After adding the package reference, open the module class of the project (eg: `{ProjectName}ApplicationModule`) and add the below code to the `DependsOn` attribute.

```csharp
[DependsOn(
  //...
  typeof(AbpPaymentApplicationModule)
)]
```

### Supported Gateway Packages

In order to use a Payment Gateway, you need to add related NuGet packages to your related project as explained in Manual Installation section above and add ```DependsOn``` to your related module. For example, if you don't want to use PayU, you don't have to use its NuGet packages. 

After adding packages of a payment gateway to your application, you also need to configure global payment module options and options for the payment modules you have added. See the Options section below.

## Packages

This module follows the [module development best practices guide](https://docs.abp.io/en/abp/latest/Best-Practices/Index) and consists of several NuGet and NPM packages. See the guide if you want to understand the packages and relations between them.

You can visit [Payment module package list page](https://abp.io/packages?moduleName=Volo.Payment) to see list of packages related with this module.

## User interface

### Pages

#### Payment gateway selection

This page allows selecting a payment gateway. If there is one payment gateway configured for final application, this page will be skipped.

![payment-gateway-selection](../images/payment-gateway-selection.png)

#### PayU prepayment page

This page is used to send Name, Surname and Email Address of user to PayU.

![payment-payu-prepayment-page](../images/payment-payu-prepayment-page.png)

## Options

### PaymentOptions

`PaymentOptions` is used to store list of payment gateways. You don't have to configure this manually for existing payment gateways. You can, however, add a new gateway like below;

````csharp
Configure<PaymentOptions>(options =>
{
	options.Gateways.Add(
		new PaymentGatewayConfiguration(
			"MyPaymentGatewayName",
			new FixedLocalizableString("MyPaymentGatewayName"),
			typeof(MyPaymentGateway)
		)
	);
});
````

`AbpIdentityAspNetCoreOptions` properties:

* `PaymentGatewayConfigurationDictionary`: List of gateway configuration.
  * ```Name```: Name of payment gateway.
  * ```DisplayName```: DisplayName of payment gateway.
  * ```PaymentGatewayType```: type of payment gateway.
  * ```Order```: Order of payment gateway.

### PaymentWebOptions

```PaymentWebOptions``` is used to configure web application related configurations.

* ```CallbackUrl```: Final callback URL for internal payment gateway modules to return. User will be redirected to this URL on your website.
* ```RootUrl```: Root URL of your website.
* ```GatewaySelectionCheckoutButtonStyle```: CSS style to add Checkout button on gateway selection page. This class can be used for tracking user activity via 3rd party tools like Google Tag Manager.
* ```PaymentGatewayWebConfigurationDictionary```:  Used to store web related payment gateway configuration.
  * ```Name```: Name of payment gateway.
  * ```PrePaymentUrl```: URL of the page before redirecting user to payment gateway for payment.
  * ```PostPaymentUrl```: URL of the page when user redirected back from payment gateway to your website. This page is used to validate the payment mostly.
  * ```Order```: Order of payment gateway for gateway selection page.
  * ```Recommended```: Is payment gateway is recommended or not. This information is displayed on payment gateway selection page.
  * ```ExtraInfos```: List of informative strings for payment gateway. These texts are displayed on payment gateway selection page.

### PayuOptions

```PayuOptions``` is used to configure PayU payment gateway options.

* ```Merchant```: Merchant code for PayU account.
* ```Signature```: Signature of Merchant.
* ```LanguageCode```: Language of the order. This will be used for notification email that are sent to the client, if available.
* ```CurrencyCode```: Currency code of order (USD, EUR, etc...).
* ```VatRate```: Vat rate of order.
* ```PriceType```: Price type of order (GROSS or NET).
* ```Shipping```: A positive number indicating the price of shipping.
* ```Installment```: The number of installments. It can be an integer between 1 and 12.
* ```TestOrder```: Is the order a test order or not (true or false).
* ```Debug```: Writes detailed log on PAYU side.
* ```Recommended```: Is payment gateway is recommended or not. This information is displayed on payment gateway selection page.
* ```ExtraInfos```: List of informative strings for payment gateway. These texts are displayed on payment gateway selection page.
* ```PrePaymentCheckoutButtonStyle```: Css style to add Checkout button on PayU prepayment page. This class can be used for tracking user activity via 3rd party tools like Google Tag Manager.

### TwoCheckoutOptions

```TwoCheckoutOptions``` is used to configure TwoCheckout payment gateway options.

* ```Signature```: Signature of Merchant's 2Checkout account.
* ```CheckoutUrl```: 2Checkout checkout URL (it must be set to https://secure.2checkout.com/order/checkout.php).
* ```LanguageCode```: Language of the order. This will be used for notification email that are sent to the client, if available.
* ```CurrencyCode```: Currency code of order (USD, EUR, etc...).
* ```Recommended```: Is payment gateway is recommended or not. This information is displayed on payment gateway selection page.
* ```ExtraInfos```: List of informative strings for payment gateway. These texts are displayed on payment gateway selection page.

### StripeOptions

```StripeConsts```: is used to configure Stripe payment gateway options.

* ```PublishableKey```: Publishable Key for Stripe account.
* ```SecretKey```: Secret Key for Stripe account.
* `WebhookSecret`: Used for handle webhooks. You can get if from [Stripe Dashboard](https://dashboard.stripe.com/webhooks). If you don't use subscription & recurring payment it's not necessary.
* ```Currency```: Currency code of order (USD, EUR, etc..., see [Stripe docs](https://stripe.com/docs/currencies) for the full list). Its default value is USD.
* ```Locale```: Language of the order. Its default value is 'auto'.
* ```PaymentMethodTypes```:  A list of the types of payment methods (e.g., card) this Checkout session can accept. See https://stripe.com/docs/payments/checkout/payment-methods. Its default value is 'card'.
* ```Recommended```: Is payment gateway is recommended or not. This information is displayed on payment gateway selection page.
* ```ExtraInfos```: List of informative strings for payment gateway. These texts are displayed on payment gateway selection page.

### PayPalOptions

```PayPalOptions``` is used to configure PayPal payment gateway options.

* ```ClientId```: Client Id for the PayPal account.
* ```Secret``` Secret for the PayPal account.
* ```CurrencyCode```: Currency code of order (USD, EUR, etc...).
* ```Environment```: Payment environment. ("Sandbox" or "Live", default value is "Sandbox")
* ```Locale```: PayPal-supported language and locale to localize PayPal checkout pages. See https://developer.paypal.com/docs/api/reference/locale-codes/.

### IyzicoOptions

```IyzicoOptions``` is used to configure Iyzico payment gateway options.

* ```BaseUrl```: Base API URL for the Iyzico (ex: https://sandbox-api.iyzipay.com). 
* ```ApiKey``` Api key for the Iyzico account.
* ```SecretKey ``` Secret for the Iyzico account.
* ```Currency```: Currency code for the order (USD, EUR, GBP and TRY can be used).
* ```Locale```: Language of the order.
* ```InstallmentCount```: Installment count value. For single installment payments it should be 1 (valid values: 1, 2, 3, 6, 9, 12).
* ```Recommended```: Is payment gateway is recommended or not. This information is displayed on payment gateway selection page.
* ```ExtraInfos```: List of informative strings for payment gateway. These texts are displayed on payment gateway selection page.
* ```PrePaymentCheckoutButtonStyle```: CSS style to add Checkout button on Iyzico prepayment page. This class can be used for tracking user activity via 3rd party tools like Google Tag Manager.

Instead of configuring options in your module class, you can configure it in your appsettings.json file like below;

```json
"Payment": {
    "Payu": {
      "Merchant": "TEST",
      "Signature": "SECRET_KEY",
      "LanguageCode": "en",
      "CurrencyCode": "USD",
      "VatRate": "0",
      "PriceType": "GROSS",
      "Shipping": "0",
      "Installment": "1",
      "TestOrder": "1",
      "Debug": "1"
    },
    "TwoCheckout": {
      "Signature": "SECRET_KEY",
      "CheckoutUrl": "https://secure.2checkout.com/order/checkout.php",
      "LanguageCode": "en",
      "CurrencyCode": "USD",
      "TestOrder": "1"
    },
    "PayPal": {
      "ClientId": "CLIENT_ID",
      "Secret": "SECRET",
      "CurrencyCode": "USD",
      "Environment": "Sandbox",
      "Locale": "en_US"
    },
    "Stripe": {
      "PublishableKey": "PUBLISHABLE_KEY",
      "SecretKey": "SECRET_KEY",
      "PaymentMethodTypes": ["alipay"]
    },
    "Iyzico": {
      "ApiKey": "API_KEY",
      "SecretKey": "SECRET_KEY",
      "BaseUrl": "https://sandbox-api.iyzipay.com",
      "Locale": "en",
      "Currency": "USD"
    }
  }
```

## Internals

### Domain layer

#### Aggregates

This module follows the [Entity Best Practices & Conventions](https://docs.abp.io/en/abp/latest/Best-Practices/Entities) guide.

##### PaymentRequest

A payment request represents a request for a payment in the application.

* `PaymentRequest` (aggregate root): Represents a payment request in the system.
  * `Products` (collection): List of products for payment request.
  * `State` : State of payment request (can be Waiting, Completed or Failed).
  * `Currency` : Currency code of payment request (USD, EUR, etc...).
  * `Gateway` : Name of payment gateway used for this payment request.
  * ```FailReason```: Reason for failed payment requests.

#### Repositories

This module follows the [Repository Best Practices & Conventions](https://docs.abp.io/en/abp/latest/Best-Practices/Repositories) guide.

Following custom repositories are defined for this module:

* `IPaymentRequestRepository`

### Application layer

#### Application services

* `PaymentRequestAppService` (implements `IPaymentRequestAppService`): Used to create payment requests and access payment request details.

### Database providers

#### Common

##### Table / collection prefix & schema

All tables/collections use the `Pay` prefix by default. Set static properties on the `PaymentDbProperties` class if you need to change the table prefix or set a schema name (if supported by your database provider).

##### Connection string

This module uses `AbpPayment` for the connection string name. If you don't define a connection string with this name, it fallbacks to the `Default` connection string.

See the [connection strings](https://docs.abp.io/en/abp/latest/Connection-Strings) documentation for details.

#### Entity Framework Core

##### Tables

* **PayPaymentRequests**
  * **AbpRoleClaims**
    * PayPaymentRequestProducts

#### MongoDB

##### Collections

* **PayPaymentRequests**

## Distributed Events

This module doesn't define any additional distributed event. See the [standard distributed events](https://docs.abp.io/en/abp/latest/Distributed-Event-Bus).

## Sample Usage

In order to initiate a payment process, inject ```IPaymentRequestAppService```, create a payment request using it's ```CreateAsync``` method and redirect user to gateway selection page with the created payment request's Id. Here is a sample Razor Page code which starts a payment process on it's OnPost method.

```c#
public class IndexModel: PageModel
    {
        private readonly IPaymentRequestAppService _paymentRequestAppService;

        public IndexModel(IPaymentRequestAppService paymentRequestAppService)
        {
            _paymentRequestAppService = paymentRequestAppService;
        }

        public virtual async Task<IActionResult> OnPost()
        {
            var paymentRequest = await _paymentRequestAppService.CreateAsync(new PaymentRequestCreateDto()
            {
                Products = new List<PaymentRequestProductCreateDto>()
                {
                    new PaymentRequestProductCreateDto
                    {
                        Code = "Product_01",
                        Name = "LEGO Super Mario",
                        Count = 2,
                        UnitPrice = 60,
                        TotalPrice = 200
                    }
                }
            });

            return LocalRedirectPreserveMethod("/Payment/GatewaySelection?paymentRequestId=" + paymentRequest.Id);
        }
    }
```

If the payment is successful, payment module will return to configured ```PaymentWebOptions.CallbackUrl```. The main application can take necessary actions for a successful payment (Activating a user account, triggering a shipment start process etc...).