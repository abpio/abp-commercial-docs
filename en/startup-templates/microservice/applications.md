# Microservice Startup Template Applications

> This startup template is not feature complete and well documented yet. However, we found it valuable to share with the ABP Commercial customers. That doesn't mean it is not production ready. You can start to develop your real solution today. But we will improve it and write more documentation for different development and deployment scenarios.

## Authentication Server

> The Authentication Server (named as AuthServer) is a web application that is used as the single sign-on authentication server. It hosts the public account pages such as login, register, forgot password, two factor authentication, profile management... pages, OAuth endpoints and authentication related APIs. All applications and services use this application as a central authority for the authentication.

### Authentication & Authorization

AuthServer is the single sign-on and single sign-off server of the microservice template. Authentication and authorization between applications is handled by AuthServer which is built upon OpenId certified [IdentityServer 4](https://identityserver4.readthedocs.io/en/latest/). 

AuthServer references to [Account Module](https://docs.abp.io/en/commercial/latest/modules/account) that `Account.Pro.Public.Web` package serves the pages like login, sign-out, external login providers etc. and `Account.Pro.Public.Application` package hosts the implementations.

Also, Account Module functionality is split between **microservices**;

- *Identity* and *IdentityServer* related data is kept in **IdentityService microservice**
- *Account management* and *profile management* related data is kept in **AdministrationService microservice** 
- *Tenant* login related data is kept in **SaasService microservice**

AuthServer makes directly database requests to these microservices to reach related data for operating successfully. Hence depends on `IdentityService EntityFrameworkCore`, `AdministrationService EntityFrameworkCore` and `SaasService EntityFrameworkCore` modules and also has the configuration of related connection strings in the *appsettings*.

### Data Seed

AuthServer needs initial data such as identityserver *clients*, *api resources*, api scopes etc and admin user to operate when the microservice stack starts running. To kick start the process, AuthService uses [Abp Data Seeding](https://docs.abp.io/en/abp/latest/Data-Seeding) to add the required initial data. This information can be found in **IdentityServerDataSeeder**. *IdentityServerDataSeedContributor* is responsible to run the data seeder. Both files are located under shared **DbMigrator** project. 

It is a good practice to keep your *IdentityServerDataSeeder* **up to date** whenever you expand your microservice solution with new api resources and clients.

#### Api Resources

> Api Resources are typically http api endpoints that clients wants to access. All the microservices are added as Api Resources

```csharp
private async Task CreateApiResourcesAsync()
{
    ...
    await CreateApiResourceAsync("IdentityService", commonApiUserClaims);
    await CreateApiResourceAsync("AdministrationService", commonApiUserClaims);
    await CreateApiResourceAsync("SaasService", commonApiUserClaims);
    await CreateApiResourceAsync("ProductService", commonApiUserClaims);
}
```

These are the **name** of the resources.

#### Api Scopes

> Api Scopes can be considered as one-to-many **parts** of **a resource** that represents what a client application is **allowed to** do. They are mostly defined as *myApi.read*, *myApi.write* etc.. for authorization purposes. Abp uses [Permission Management](https://docs.abp.io/en/abp/latest/UI/Angular/Permission-Management) and delegates to authorization to permission management. Hence declaring one scope for each api resource

```csharp
private async Task CreateApiScopesAsync()
{
    await CreateApiScopeAsync("IdentityService");
    await CreateApiScopeAsync("AdministrationService");
    await CreateApiScopeAsync("SaasService");
    await CreateApiScopeAsync("ProductService");
}
```

#### Clients

> Clients are the applications that want to reach *api resources* via allowed interactions (*grant types*) with the token server by representing a list (*scopes*) of  what they request to do.

There are different clients seeded for AuthServer application;

- **Swagger Clients:** These are the gateways added as clients using  `authorization_code` grant type. While *WebGateway* and *Internal Gateway* are allowed for all the scopes, *PublicWeb Gateway* is only allowed to `ProductService` scope default.

- **Back-Office Clients:** 

  - **Web** Client is the Razor/MVC application client using `hybrid` grant type. This client has all the scope allowance as default.
  - **Angular** is the spa application client using `authorization_code` grant type. This client has all the scope allowance as default.
  - **Blazor** Client is the Blazor WebAssembly application client using `authorization_code` grant type. This client has all the scope allowance as default.
  - **Blazor Server** is the application client using `hybrid` grant type. This client has all the scope allowance as default.

- **Public/Landing Page Client:** Public Web application is a Razor/MVC application client using `hybrid` grant type. This client has `AdministrationService` and `ProductService` scope allowance as default.

- **Service Clients**: These are the server-to-server interactions between services. This is a `client_credentials` grant type and there is no user or user information involved in this flow. 

  As default, **AdministrationService** is declared as a client. AdministrationService requires list of user data from *IdentityService*. Since this endpoint is authorized with a permission, required permission is also granted on the client creation. Granted permission will be seen with ProviderName `C` under *AbpPermissionGrants* table in Administration database. That indicates this is a `client_credential` given permission.

  ```csharp
  //Administration Service Client
  await CreateClientAsync(
      name: "MyProjectName_AdministrationService",
      scopes: commonScopes.Union(new[]
      {
          "IdentityService" // Allowed scope to make request
      }),
      grantTypes: new[] {"client_credentials"},
      secret: "1q2w3e*".Sha256(),
      permissions: new[] {IdentityPermissions.Users.Default}
  );
  ```

### Deployment Configurations

> Since AuthServer is an application based on IdentityServer4; all the deployment requirements are also valid for AuthServer application.

#### Signing Certificate

AuthServer application uses [developer signing certificates option]([abp/AbpIdentityServerBuilderOptions.cs at dev · abpframework/abp (github.com)](https://github.com/abpframework/abp/blob/dev/modules/identityserver/src/Volo.Abp.IdentityServer.Domain/Volo/Abp/IdentityServer/AbpIdentityServerBuilderOptions.cs#L29)) for default development environment. Using developer signing certificates may cause *IDX10501: Signature validation failed* error. 

To change the signing credential for staging/production, update your application module with IIdentityServerBuilder pre-configuration.

```csharp
public override void PreConfigureServices(ServiceConfigurationContext context)
{
	PreConfigure<IIdentityServerBuilder>(builder =>
	{
    	builder.AddSigningCredential(...);	
	});
}
```

#### Running AuthServer on HTTPS

AuthServer must be publicly reachable and available over https. [Microsoft recommendation for enforcing https](https://docs.microsoft.com/en-us/aspnet/core/security/enforcing-ssl?view=aspnetcore-5.0&tabs=visual-studio#require-https) is using **Https** and **Hsts** together for https redirection;

```csharp
public override void OnApplicationInitialization(ApplicationInitializationContext context)
{
    var app = context.GetApplicationBuilder();
    var env = context.GetEnvironment();
    ...
    if (!env.IsDevelopment())
    {
        app.UseErrorPage();
        app.UseHsts();
    }
    
    app.UseHttpsRedirection();    
    app.UseCors(DefaultCorsPolicyName);
    ...
}
```

#### Running AuthServer Behind Load Balancer


Configure *Forewarded Headers* when running AuthServer behind Load Balancers;

```csharp
 public override void ConfigureServices(ServiceConfigurationContext context)
 {
     var hostingEnvironment = context.Services.GetHostingEnvironment();
     var configuration = context.Services.GetConfiguration();
     ...
     context.Services.Configure<ForwardedHeadersOptions>(options =>
     {
         options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
     });
 }
public override void OnApplicationInitialization(ApplicationInitializationContext context)
{
    var app = context.GetApplicationBuilder();
    var env = context.GetEnvironment();
    ...
    if (!env.IsDevelopment())
    {
        app.UseErrorPage();          
	    app.UseForwardedHeaders();
        app.UseHsts();
    }
    
    app.UseHttpsRedirection();    
    app.UseCors(DefaultCorsPolicyName);
    ...
}
```

#### Running AuthServer on Kubernates Pods

You may not be able to set certificates in pods and have to use http inside pods. In this case, use midware for redirection to https

```csharp
public override void OnApplicationInitialization(ApplicationInitializationContext context)
{
    var app = context.GetApplicationBuilder();
    var env = context.GetEnvironment();
    ...
    if (!env.IsDevelopment())
    {
        app.UseErrorPage();
        app.UseHsts();
    }
    
    app.Use((httpContext, next) =>
    {
        httpContext.Request.Scheme = "https";
        return next();
    });
    
    app.UseHttpsRedirection();    
    app.UseCors(DefaultCorsPolicyName);
    ...
}
```

If headers are not forwarded as expected, enable logging. Check [Microsoft Troubleshoot Guide](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/proxy-load-balancer?view=aspnetcore-5.0#troubleshoot) fore more information.



## Web Application (Back-office)

Varieties (MVC,Angular,Blazor,Blazor.Server) hangi gateway'ler




## Public Application



## Adding Tests to Applications

All the application has their respected solutions under app folder. Create a **test** folder where the *src* folder is located.  Open the application solution and add a new solution folder named **test**.

Add the test projects required by your needs under this folder. Check [The Test Projects Docs](https://docs.abp.io/en/abp/latest/Testing) for more information.