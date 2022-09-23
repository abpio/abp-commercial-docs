# Add Module to Existing Microservices

> This documentation introduces guidance for adding the module to the microservice for your microservice startup template. Eventually, ever module has own documantion and implemantion steps, even so, some steps can be different for microservice template. We will demonstrate the different steps to run microservice template properly. 

## Adding a module

You can adding any module to the  your sub microservice into your microservice solution by using the ABP CLI. Use the following command to add the module whatever you want:

```powershell
abp add-module ModuleName
```

After completing the module documantion steps you can build your solution and continue to this documantion.

```bash
dotnet build
```

> Note: We already have the documantion to create new sub service to microservice template hence we won't demonstrate these steps. We accepted you did these configurations. You can have a look [here](https://docs.abp.io/en/commercial/latest/startup-templates/microservice/add-microservice). 

## Configure CORS

Normally you don't need to configure CORS for your own module, but in here you should configure it in `YourServiceHttpApiHostModule` by adding this line.

```csharp
app.UseCors();
```

Also, you should add to your web project url into your `appsetttings.json` file under `YourService.HttpApi.Host`

```json
"App": {
    "SelfUrl": "https://localhost:YourProjectPort",
    "CorsOrigins": "https://localhost:YourWebProjectPort"
},
```

Note: If you need more configuration you can use `comma` as a seperator. 

## Converting the dynamic proxy to static proxy

As you know, ABP Framework supports [dynamic](https://docs.abp.io/en/abp/latest/UI/AspNetCore/Dynamic-JavaScript-Proxies) and [static](https://docs.abp.io/en/abp/latest/UI/AspNetCore/Static-JavaScript-Proxies) proxies. Both has advantages and disadvantages to each other. We prefer dynamic proxy in the modules, but in the microservice template have should used the static proxy. Firstly you configure static proxy and create them manually.

```csharp
public class YourServiceHttpApiClientModule : AbpModule
{
    public override void ConfigureServices(ServiceConfigurationContext context)
    {
        context.Services.AddHttpClientProxies(typeof(YourServiceApplicationContractsModule).Assembly,
            YourServiceRemoteServiceConsts.RemoteServiceName);

        Configure<AbpVirtualFileSystemOptions>(options =>
        {
            options.FileSets.AddEmbedded<YourServiceHttpApiClientModule>();
        });
    }
}
```

Need to convert `AddHttpClientProxies` to `AddStaticHttpClientProxies`. Then you should create the static proxies in `EShopOnAbp.YourService.HttpApi.Client` by using the following command line

> abp generate-proxy -t csharp --module YourModuleName -u http://localhost:YourProjectPort/

## Cofigure Gateways
Microservice template project has two gateway projects.

- **WebGateway** 
- **PublicWebGateway**

The first one is for admin side and the other one is for public side. If you would like to use your module in both side, you should configure ocelot side in both projects. There is an example one and you need to configure it according to your requirenments.

```json
{
    "ServiceKey": "YourModuleName",
    "DownstreamPathTemplate": "/api/your-module/{everything}",
    "DownstreamScheme": "https",
    "DownstreamHostAndPorts": [
        {
            "Host": "localhost",
            "Port": YourProjectPort
        }
    ],
    "UpstreamPathTemplate": "/api/your-module/{everything}",
    "UpstreamHttpMethod": [ "Put", "Delete", "Get", "Post" ]
}
```

> You can make different configurations for each method or endpoint for your service and add QoS configurations based on your business requirements. You can check [ocelot documentation](https://ocelot.readthedocs.io/en/latest/) for more.

## Editing Typo

After completing adding your module, you should see some changes on different page to the inject dependencies. In `AdministrationServiceDbContext` the overrided method `OnModelCreating` may be a typo because of parameter name. For example for CmsKit it comes with `builder` parameter name but in this class the parameter name is `modelBuilder`. It's enough to change it with the correct one.

```chsarp
builder.ConfigureCmsKit();
```

## Implementing IYourModuleDbContext

After running adding module command, you should see your migration files under the `Migrations` in the `EntityFrameworkCore` project. If it's empty please make sure whether implementing `IModuleDbContext` or not. Once implementing, you should warn to import your entites here. Now, you're ready to create your migration properly. If you create your migrations properly, just you need to run the project. ABP Framework will handle it automatically.

Now you should configure `AbpDbContextOptions` in the `YourServiceEntityFrameworkCoreModule` under the `EntityFrameworkCore` project. Already you should see the configuration for your service in `ConfigureServices` for your project, but also need to configure it for the new module projects.

```csharp
public override void ConfigureServices(ServiceConfigurationContext context)
{
    //The other congiurations
    options.Configure<YourModuleDbContext>(c =>
    {
        c.UseSqlServer(b => //It may change up to your db
        {
            b.MigrationsHistoryTable("__YourService_Migrations");
        });
    });
}
``` 

## Using Component

If you want to use the component which defined in the module in your web page, you should add its releated page on NuGet. After you adding your package you should add the dependency to `YourModulePublicWebModule` as the following code

```csharp
typeof(YourModulePublicWebModule)
public class YourServicePublicWebModule : AbpModule
{
    //Configurations
}
```

Now you can your components in your page for razor page.
```csharp
@await Component.InvokeAsync(typeof(YourViewComponent), new {key = "value"})
```

Now you should see your component, but probably you will the following error once trying to submit. Because still you need some configurations regarding user syncrozation.

![comment-component](../images/comment-component.png)
![user-error](../images/user-error.png)

## Configured External User

To submit some components you need to be log in the system. Already you logged in the system but your module database can be empty hence we need to check `IdentityService` for this user. 

Firstly you should add service client to `IdentityServerDataSeeder` under the `IdentityService.HttpApi.Host`

```csharp
//The other configurations
await CreateClientAsync(
    name: "YourServiceName",
    scopes: commonScopes.Union(new[]
    {
        "IdentityService"
    }),
    grantTypes: new[] { "client_credentials" },
    secret: "1q2w3e*".Sha256(),
    permissions: new[] { } //This changes up to your permission
);
```

Then change your `appsetting.json` by adding the follow code under the `YourService.HttpApi.Host`
```json
"RemoteServices": {
    "AbpIdentity": {
    "BaseUrl": "https://localhost:IdentityServicePort"
    }
},
"IdentityClients": {
    "Default": {
    "GrantType": "client_credentials",
    "ClientId": "YourService",
    "ClientSecret": "1q2w3e*",
    "Authority": "https://localhost:AuthServerPort",
    "Scope": "IdentityService"
    }
}
```

Finally you need to map the YourModule to YourService in `DocSharedHostingModule` under the `Shared.Hosting`
```csharp
public override void ConfigureServices(ServiceConfigurationContext context)
{
    Configure<AbpDbConnectionOptions>(options =>
    {
        options.Databases.Configure("YourService", database =>
        {
            database.MappedConnections.Add("YourModuleName");
            database.MappedConnections.Add("YourService");
        });
    }
}
```

If you applied all steps correctly, you should see the below output.

![first-comment](../images/first-comment.png)

Regards.

