# Microservice Startup Template API Gateways

> API Gateways are used as single entry point to the microservices. Abp microservice startup template uses [Ocelot .Net Api Gateway](https://github.com/ThreeMammals/Ocelot) library. Through this library, gateways have the functionality of *routing*; *rate limiting*, *retry policies* etc. For more, check out [Ocelot Documentations](https://ocelot.readthedocs.io/en/latest/).

There are 3 different gateways are presented in the microservice startup template.

- **Web Gateway** is located under *gateways/web* folder. This API Gateway uses BFF pattern and redirects requests from Web application (MVC/Agular/Blazor) to Authentication Server or microservices.
- **Public Web Gateway**  is located under *gateways/webpublic* folder. This API Gateway also uses BFF pattern and redirects requests from Public Web application to Authentication Server or microservices.
- **Internal Gateway** is located under *gateways/internal* folder. This API Gateway is used between microservices for inter-communication.

All gateways has their respected solutions created already and can be developed further when if required without opening the whole template solution. It is also possible to add test projects to gateways. Create a **test** folder where the *src* folder is located.  Open the application solution and add a new solution folder named **test**.

Add the test projects required by your needs under this folder. Check [The Test Projects Docs](https://docs.abp.io/en/abp/latest/Testing) for more information.

![overall-applications](../../images/overall-applications.png)

To compare microservice template to tiered application template: Gateway is an **API.Host** project that proxies all the requests to related microservices.  

Gateways are depended to HttpApi layer of the microservices they are redirecting to.

All gateways depends on **SharedHostingGatewayModule** which implements default **Ocelot** and **Swagger** with authorization configuration. For more, check [Shared Modules#HostingGateways](https://docs.abp.io/en/abp/latest/API/Dynamic-CSharp-API-Clients)

## Backend for Frontend Pattern (BFF)

While Api Gateway provides a **single point of entry** to system, Backend for Frontend pattern defines **each client with an individual API**. Abp Microservice template uses BFF pattern thus each application has its own web gateway.

![gateway-bff](../../images/gateway-bff.png)

If you are planning to add your custom client (such as mobile application), it is recommended to add a new gateway for that specific client since each client's requests will probably be different.

## Web Gateway

Web Gateway is used to connect the **Web** (back-office) application to microservices. This is done by setting this gateway as default **RemoteService** in Web application appsettings. ([See here](applications.md#remote service calls - web gateway))

### Module Configuration and Routing

As default, this gateway proxies each request to related microservice and redirects the account related requests to AuthServer;

- **IdentityService:** Depends on `IdentityServiceHttpApiModule` and re-routes 

  - `/api/identity/{everything}`
  - `/api/identity-server/{everything}`
  - `/api/account-admin/{everything}`

  to `localhost:44388` (IdentityService) in appsettings configuration.

- **AdministrationService:** Depends on `AdministrationServiceHttpApiModule` and re-routes

  -  `/api/abp/{everything}`  [application configuration endpoint](https://docs.abp.io/en/abp/latest/API/Application-Configuration)
  - `/api/audit-logging/{everything}`
  - `/api/language-management/{everything}`
  - `/api/text-template-management/{everything}`
  - `/api/feature-management/{everything}`
  - `/api/permission-management/{everything}`
  - `/api/setting-management/{everything}`
  - `/api/lepton-theme-management/{everything}`

   to `localhost:44367` (AdministrationService) in appsettings configuration.

- **SaasService:** Depends on `SaasServiceHttpApiModule` and re-routes

  -  `/api/saas/{everything}`

   to `localhost:44381` (SaasService) in appsettings configuration,

- **ProductService:** Depends on `ProductServiceHttpApiModule` and re-routes

  - `/api/product-service/{everything}`

   to `localhost:44361` (ProductService) in appsettings configuration.

- **AuthServer:** Depends on `AbpAccountPublicHttpApiModule` and re-routes

  -  `/api/account/{everything}` (login page etc requests)

  to `localhost:44322` (Authentication Server) in appsettings configuration.

### Authorization Configuration

Web Gateway has swagger with authorization configuration to make *authorization_code* interaction with AuthServer to be able to get authorized scopes:

```csharp
SwaggerWithAuthConfigurationHelper.Configure(
    context: context,
    authority: configuration["AuthServer:Authority"],
    scopes: new Dictionary<string, string> /* Requested scopes for authorization code request and descriptions for swagger UI only */
    {
        {"IdentityService", "Identity Service API"},
        {"AdministrationService", "Administration Service API"},
        {"SaasService", "Saas Service API"},
        {"ProductService", "Product Service API"}
    },
    apiTitle: "Web Gateway API"
);
```

As default, Web Gateway makes requests to all api scopes that are already allowed when the `WebGateway_Swagger` client is being created in [IdentityServer configuration](#identityServer configuration). To be able to make the request, required information is found under **AuthServer** section in `appsettings.json`:

```json
"AuthServer": {
  "Authority": "https://localhost:44322",
  "RequireHttpsMetadata": "true",
  "SwaggerClientId": "WebGateway_Swagger",
  "SwaggerClientSecret": "1q2w3e*"
},
```

 Web Gateway authorization screen can be seen below:

![web-gateway-auth](../../images/web-gateway-auth.PNG)

The automatically filled `client_id` and `client_secret` fields are set under **WebGatewayModule** `OnApplicationInitialization`:

```csharp
app.UseSwaggerUI(options =>
{
    options.SwaggerEndpoint("/swagger/v1/swagger.json", "Web Gateway API");
    var configuration = context.ServiceProvider.GetRequiredService<IConfiguration>();
    options.OAuthClientId(configuration["AuthServer:SwaggerClientId"]);
    options.OAuthClientSecret(configuration["AuthServer:SwaggerClientSecret"]);
});
```

> If you add a new microservice and want to use in your Web application; you need to [update this gateway configuration](add-microservice.md#updating gateways) and [IdentityServer configuration](#identityServer configuration).

## Public Web Gateway

Public Web Gateway is used to connect the **Public  Web** (landing page) application to microservices. This is done by setting this gateway as default **RemoteService** in Public Web application appsettings. ([See here](applications.md#remote service calls - publicweb gateway))

### Module Configuration and Routing

As default, this gateway proxies each request to related microservice and redirects the account related requests to AuthServer;

- **ProductService:** Depends on `ProductServiceHttpApiModule` and re-routes

  - `/api/product-service/{everything}`

   to `localhost:44361` (ProductService) in appsettings configuration.

- **AuthServer:** Depends on `AbpAccountPublicHttpApiModule` and re-routes

  -  `/api/account/{everything}` (login page etc requests)

  to `localhost:44322` (Authentication Server) in appsettings configuration.

### Authorization Configuration

Web Gateway has swagger with authorization configuration to make *authorization_code* interaction with AuthServer to be able to get authorized scopes:

```csharp
SwaggerWithAuthConfigurationHelper.Configure(
    context: context,
    authority: configuration["AuthServer:Authority"],
    scopes: new
    Dictionary<string, string> /* Requested scopes for authorization code request and descriptions for swagger UI only */
    	{
        	{"ProductService", "Product Service API"}
    	},
    apiTitle: "Public Web Gateway API"
);
```

As default, PublicWeb Gateway makes requests to only **ProductService** scope that is already allowed when the `PublicWebGateway_Swagger` client is being created in [IdentityServer configuration](#identityServer configuration). To be able to make the request, required information is found under **AuthServer** section in `appsettings.json`:

```json
"AuthServer": {
  "Authority": "https://localhost:44322",
  "RequireHttpsMetadata": "true",
  "SwaggerClientId": "PublicWebGateway_Swagger",
  "SwaggerClientSecret": "1q2w3e*"
},
```

 PublicWeb Gateway authorization screen can be seen below:

![public-web-gateway-auth](../../images/public-web-gateway-auth.png)

The automatically filled `client_id` and `client_secret` fields are set under **PublicWebGatewayModule** `OnApplicationInitialization`:

```csharp
app.UseSwaggerUI(options =>
{
    options.SwaggerEndpoint("/swagger/v1/swagger.json", "Web Gateway API");
    var configuration = context.ServiceProvider.GetRequiredService<IConfiguration>();
    options.OAuthClientId(configuration["AuthServer:SwaggerClientId"]);
    options.OAuthClientSecret(configuration["AuthServer:SwaggerClientSecret"]);
});
```

> If you add a new microservice and want to use in your PublicWeb application; you need to [update this gateway configuration](add-microservice.md#updating gateways) and [IdentityServer configuration](#identityServer configuration).

## Internal Gateway

Apart from other web gateways, Internal Gateway is an api gateway to locate and redirect service requests in microservice inter-communication.

### Module Configuration and Routing

As default, this gateway proxies each request to related microservice and redirects the account related requests to AuthServer;

- **IdentityService:** Depends on `IdentityServiceHttpApiModule` and re-routes 

  - `/api/identity/{everything}`
  - `/api/identity-server/{everything}`
  - `/api/account-admin/{everything}`

  to `localhost:44388` (IdentityService) in appsettings configuration.

- **AdministrationService:** Depends on `AdministrationServiceHttpApiModule` and re-routes

  -  `/api/abp/{everything}`  [application configuration endpoint](https://docs.abp.io/en/abp/latest/API/Application-Configuration)
  - `/api/audit-logging/{everything}`
  - `/api/language-management/{everything}`
  - `/api/text-template-management/{everything}`
  - `/api/feature-management/{everything}`
  - `/api/permission-management/{everything}`
  - `/api/setting-management/{everything}`
  - `/api/lepton-theme-management/{everything}`

   to `localhost:44367` (AdministrationService) in appsettings configuration.

- **SaasService:** Depends on `SaasServiceHttpApiModule` and re-routes

  -  `/api/saas/{everything}`

   to `localhost:44381` (SaasService) in appsettings configuration,

- **ProductService:** Depends on `ProductServiceHttpApiModule` and re-routes

  - `/api/product-service/{everything}`

   to `localhost:44361` (ProductService) in appsettings configuration.

- **AuthServer:** Depends on `AbpAccountPublicHttpApiModule` and re-routes

  -  `/api/account/{everything}` (login page etc requests)

  to `localhost:44322` (Authentication Server) in appsettings configuration.

### Authorization Configuration

Internal Gateway has swagger with authorization configuration to make *authorization_code* interaction with AuthServer to be able to get authorized scopes:

```csharp
SwaggerWithAuthConfigurationHelper.Configure(
    context: context,
    authority: configuration["AuthServer:Authority"],
    scopes: new Dictionary<string, string> /* Requested scopes for authorization code request and descriptions for swagger UI only */
    {
        {"IdentityService", "Identity Service API"},
        {"AdministrationService", "Administration Service API"},
        {"SaasService", "Saas Service API"},
        {"ProductService", "Product Service API"}
    },
    apiTitle: "Internal Gateway API"
);
```

As default, Internal Gateway makes requests to all api scopes that are already allowed when the `InternalGateway_Swagger` client is being created in [IdentityServer configuration](#identityServer configuration). To be able to make the request, required information is found under **AuthServer** section in `appsettings.json`:

```json
"AuthServer": {
  "Authority": "https://localhost:44322",
  "RequireHttpsMetadata": "true",
  "SwaggerClientId": "InternalGateway_Swagger",
  "SwaggerClientSecret": "1q2w3e*"
},
```

 Internal Gateway authorization screen can be seen below:

![public-web-gateway-auth](../../images/internal-gateway-auth.PNG)

The automatically filled `client_id` and `client_secret` fields are set under **PublicWebGatewayModule** `OnApplicationInitialization`:

```csharp
app.UseSwaggerUI(options =>
{
    options.SwaggerEndpoint("/swagger/v1/swagger.json", "Web Gateway API");
    var configuration = context.ServiceProvider.GetRequiredService<IConfiguration>();
    options.OAuthClientId(configuration["AuthServer:SwaggerClientId"]);
    options.OAuthClientSecret(configuration["AuthServer:SwaggerClientSecret"]);
});
```

> If you add a new microservice and want to use in your microservice inter-communication; you need to [update this gateway configuration](add-microservice.md#updating gateways) and [IdentityServer configuration](#identityServer configuration).

### IdentityServer Configuration

As default, all gateways uses swagger authorization so that user can login to AuthServer and make requests to authorized api endpoints. This is done by predefining a *swagger client* in **IdentityServerDataSeeder** under `CreateSwaggerClientsAsync` method at DbMigrator project:

```csharp
private async Task CreateSwaggerClientsAsync()
{
    await CreateSwaggerClientAsync("InternalGateway", new []{"IdentityService","AdministrationService","SaasService","ProductService"});
    await CreateSwaggerClientAsync("WebGateway", new []{"IdentityService","AdministrationService","SaasService","ProductService"});
    await CreateSwaggerClientAsync("PublicWebGateway", new []{"ProductService"});
}
```

Internal Gateway and Web Gateway has allowance for all the api scopes and Public WebGateway has only  allowance for `ProductService`.

## Configurations

Some ocelot configurations (such as polly) are already provided with the microservice template as default. While all the [ocelot configurations](https://ocelot.readthedocs.io/en/latest/features/configuration.html) can be applied, some abp configurations may also be used for some common patterns.

### Aggragator Pattern

The aggregator pattern is used to combine the results of the multiple requests from services as a single response. You can use [Ocelot for Request Aggregation](https://ocelot.readthedocs.io/en/latest/features/requestaggregation.html) or manually implement your own logic in api gateway solution.

### Circuit Breakers

Circuit Breakers are used to improve the resilience and stability of the system with handling the detection of long response times or failures when calling remote services or resources. [Ocelot Quality of Service]([Quality of Service — Ocelot 1.0.0 documentation](https://ocelot.readthedocs.io/en/latest/features/qualityofservice.html)) is already implemented to be used in appsettings configurations. 

Although since it is an inter-communication between microservices, [Abp Dynamic C# API Configuration](https://docs.abp.io/en/abp/latest/API/Dynamic-CSharp-API-Clients#retry-failure-logic-polly-integration) can also be used.