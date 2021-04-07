# Microservice Startup Template Applications

> This startup template is not feature complete and well documented yet. However, we found it valuable to share with the ABP Commercial customers. That doesn't mean it is not production ready. You can start to develop your real solution today. But we will improve it and write more documentation for different development and deployment scenarios.

## Authentication Server

> The Authentication Server (named as AuthServer) is a web application that is used as the single sign-on authentication server. It hosts the public account pages such as login, register, forgot password, two factor authentication, profile management... pages, OAuth endpoints and authentication related APIs. All applications and services use this application as a central authority for the authentication.

### Authentication & Authorization

AuthServer hosts the public account pages such as login, register, forgot password etc.

Grant Types

Configurations

AuthServer Dependencies

### Data Seed

AuthServer needs initial data such as identityserver *clients*, *api resources*, api scopes etc.. to operate when the microservice stack starts running. To kick start the process, AuthService uses [Abp Data Seeding](https://docs.abp.io/en/abp/latest/Data-Seeding) to add the required initial data. This information can be found in **IdentityServerDataSeeder**. *IdentityServerDataSeedContributor* is responsible to run the data seeder. Both files are located under shared DbMigrator project. 

It is a good practice to keep your *IdentityServerDataSeeder* **up to date** whenever you expand your microservice solution with new api resources and clients.

**Api Resources** are typically http api endpoints that clients wants to access. All the microservices are added as Api Resources:

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

**Api Scopes** can be considered as one-to-many **parts** of **a resource** that represents what a client application is **allowed to** do. Since Abp uses [Permission Management](https://docs.abp.io/en/abp/latest/UI/Angular/Permission-Management), defining one scope for each api resource instead of *.read*, *.write* variants:

```csharp
private async Task CreateApiScopesAsync()
{
    await CreateApiScopeAsync("IdentityService");
    await CreateApiScopeAsync("AdministrationService");
    await CreateApiScopeAsync("SaasService");
    await CreateApiScopeAsync("ProductService");
}
```

**Clients** are the applications that want to reach api resources via allowed interactions (grant types) with the token server by representing a list (scopes) of  what they request to do.

There are different clients seeded for AuthServer application;

Swagger-Clients

Each gateway (Internal, Web, PublicWeb) is added as **swagger-clients** with the pre-defined scope allowance:

```csharp
private async Task CreateSwaggerClientsAsync()
{
    await CreateSwaggerClientAsync("InternalGateway", new []{"IdentityService","AdministrationService","SaasService","ProductService"});
    await CreateSwaggerClientAsync("WebGateway", new []{"IdentityService","AdministrationService","SaasService","ProductService"});
    await CreateSwaggerClientAsync("PublicWebGateway", new []{"ProductService"});
}
```

Gateways clients use `authorization_code` grant type and allowed scopes must be passed to `CreateSwaggerClientAsync` method. 



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

### Adding Tests

AuthServer application has it's own solution under app folder. Create a **test** folder under auth-server directory.  Open **AuthServer** solution and add a new solution folder named **test**.

Add the test projects required by your needs under this folder. Check [The Test Projects Docs](https://docs.abp.io/en/abp/latest/Testing) for more information.



## Web Application (Back-office)

Varieties (MVC,Angular,Blazor,Blazor.Server)




## Public Application


