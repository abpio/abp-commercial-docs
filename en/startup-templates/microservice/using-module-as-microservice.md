# Using a Module as Microservice

To run the initial microservice template properly, **AdministrationService** hosts required management modules such as permission-management, setting-management, audit-logging etc. However, you may need to extract one or more management system into an isolated microservice as hosted alone.
This guide explains how to extract `Audit-Logging Management` from Administration service into a different microservice called **LoggingService**.

## Adding New Logging Microservice

  Create a new microservice called LoggingService to host audit-logging management using the cli command `abp new LoggingService -t microservice-service-pro`.
Follow the guide: [Adding a new Microservice](add-microservice.md).

At the end, you should have a `logging` folder under services that contains LoggingService solution directory.

### Updating Admin Application
You need to update your admin application to request and use the newly created microservice scope.
- #### Angular
If you are using angular application, navigate to src/environments and add **LoggingService** to oAuthConfiguration **scope** object at `environment.ts` file:
```typescript
const oAuthConfig = {
  issuer: 'https://localhost:44322',
  redirectUri: baseUrl,
  clientId: 'BookStore_Angular',
  responseType: 'code',
  scope:
    'offline_access openid profile email phone AuthServer IdentityService AdministrationService SaasService ProductService LoggingService',
  requireHttps: true,
};
```

- #### Mvc and Blazor Applications
Update *OpenIdConnect* configuration at `ConfigureServices` method as below:
```csharp
.AddAbpOpenIdConnect("oidc", options =>
{
	...

	options.SaveTokens = true;
	options.GetClaimsFromUserInfoEndpoint = true;

	options.Scope.Add("role");
	options.Scope.Add("email");
	options.Scope.Add("phone");
	options.Scope.Add("AccountService");
	options.Scope.Add("IdentityService");
	options.Scope.Add("AdministrationService");
	options.Scope.Add("SaasService");
	options.Scope.Add("ProductService");
	options.Scope.Add("LoggingService");
});
```

### Adding LoggingService.Web Project

> Note: You can skip this step if you are using angular admin application

Default microservice generation to the solution does not include any ui layer. In order to host Audit-Logging Management UI separately, create new project named **LoggingService.Web**. Your LoggingService.Web.csproj should look like:
```csharp
<Project Sdk="Microsoft.NET.Sdk">

    <PropertyGroup>
        <TargetFramework>net6.0</TargetFramework>
        <RootNamespace>Acme.BookStore.LoggingService</RootNamespace>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Volo.Abp.AuditLogging.Web" Version="5.1.4"/>
        <PackageReference Include="Microsoft.Extensions.FileProviders.Embedded" Version="6.0.0"/>
    </ItemGroup>

    <ItemGroup>
        <ProjectReference Include="..\Acme.BookStore.LoggingService.Application.Contracts\Acme.BookStore.LoggingService.Application.Contracts.csproj"/>
    </ItemGroup>

</Project>

```
Also add the module class named LoggingServiceWebModule.cs:
```csharp
using Volo.Abp.AuditLogging.Web;
using Volo.Abp.Modularity;
using Volo.Abp.VirtualFileSystem;

namespace Acme.BookStore.LoggingService;
[DependsOn(
    typeof(AbpAuditLoggingWebModule),
    typeof(LoggingServiceApplicationContractsModule)
)]
public class LoggingServiceWebModule: AbpModule
{
    public override void ConfigureServices(ServiceConfigurationContext context)
    {
        Configure<AbpVirtualFileSystemOptions>(options =>
        {
            options.FileSets.AddEmbedded<LoggingServiceWebModule>();
        });
    }
}
```
Add this layer to your admin application (**Web**) as project reference:
`<ProjectReference Include="..\..\..\..\services\logging\src\Acme.BookStore.LoggingService.Web\Acme.BookStore.LoggingService.Web.csproj" />` and module dependency to the *WebModule* as:
`typeof(LoggingServiceWebModule)`

Now you are ready to extract other layers of AuditLogging.
## Extracting AuditLogging Layers

Navigate to **AdministrationService** solution and navigate to Domain.Shared layer:
- Remove `<PackageReference Include="Volo.Abp.AuditLogging.Domain.Shared" Version="5.1.4" />` project reference and add it to LoggingService.Domain.Shared project.
- Remove `typeof(AbpAuditLoggingDomainSharedModule)` module dependency and `using Volo.Abp.AuditLogging;` using from AdministrationServiceDomainSharedModule and add to **LoggingServiceDomainSharedModule**.

Repeat this extraction for all the layers below:
- Domain
- Application.Contracts
- Application
- HttpApi
- HttpApi.Client
- EntityFrameworkCore 

> Not: If you are using MongoDb for your LoggingService, add `Volo.Abp.AuditLogging.MongoDB` nuget package instead of `Volo.Abp.AuditLogging.EntityFrameworkCore`.


## Updating Web Gateway
You should have added LoggingService ocelot reRoute to web gateway at adding  new microservice steps. Now you need to update audit-logging reRoute to LoggingService instead of AdministrationService as below:
```json
{
      "DownstreamPathTemplate": "/api/audit-logging/{everything}",
      "DownstreamScheme": "https",
      "DownstreamHostAndPorts": [
        {
          "Host": "localhost",
          "Port": 44974
        }
      ],
      "UpstreamPathTemplate": "/api/audit-logging/{everything}",
      "UpstreamHttpMethod": [ "Put", "Delete", "Get", "Post" ]
    },
```
> Note: Update host and port to as same with your LoggingService. In this sample, it is localhost:44974

## Configuring Audit Logging Database
In **AdministrationServiceDbContext**, remove `IAuditLoggingDbContext` implementation and AuditLog configuration and add to **LoggingServiceDbContext**. Your updated LoggingServiceDbContext.cs should look like:
```csharp
[ConnectionStringName(LoggingServiceDbProperties.ConnectionStringName)]
public class LoggingServiceDbContext : AbpDbContext<LoggingServiceDbContext>,
    IAuditLoggingDbContext
{
    public DbSet<AuditLog> AuditLogs { get; set; }

    public LoggingServiceDbContext(DbContextOptions<LoggingServiceDbContext> options) : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);

        builder.ConfigureLoggingService();
        builder.ConfigureAuditLogging();
    }
}
```
Remove `options.ReplaceDbContext<IAuditLoggingDbContext>();` at AddDbContext configuration of **AdministrationServiceEntityFrameworkCoreModule** and add it to **LoggingServiceEntityFrameworkCoreModule** AddAbpDbContext configuration options. This will allow DI container to use LoggingServiceDbContext whenever AuditLoggingDbContext is requested.

Under **LoggingService.EntityFrameworkCore** folder, create an initial migration for AuditLogging by using dotnet ef cli: `dotnet ef migrations add "Initial"`. This should create a *Migration* folder with the creation of related AuditLogging tables.

> **Optional:** Create a new migration to remove the Auditing tables since we moved them to a different microservice database. To do that, use dotnet ef cli: `dotnet ef migrations add "Removed-Audit-Logging"` under AdministrationService.EntityFrameworkCore project.

## Updating Shared Hosting Module
You need to configure AbpAuditingLogging database connecting string to LoggingService in order to use LoggingService connection string instead of separating the connection strings.
Remove `database.MappedConnections.Add("AbpAuditLogging");` from AdministrationService database configuration and add LoggingService database configuration as below:
```csharp
options.Databases.Configure("LoggingService", database =>
{
	database.MappedConnections.Add("AbpAuditLogging");
});
```

## Updating Shared Hosting Microservice Module
To make Audit logging repository implementation available for all microservices; Add `<ProjectReference Include="..\..\services\logging\src\Acme.BookStore.LoggingService.EntityFrameworkCore\Acme.BookStore.LoggingService.EntityFrameworkCore.csproj" />` project reference to **Hosting.Microservices.csproj** and add `typeof(LoggingServiceEntityFrameworkCoreModule)` module dependency to **HostingMicroservicesModule**
## Testing Product Entity Changes
Update ProductServiceDomainModule to track all entity changes as below:
```csharp
Configure<AbpAuditingOptions>(options =>
{
	options.EntityHistorySelectors.AddAllEntities();
});
```
Update ConnectionStrings under Product.HttpApi.Host appsettings by adding LoggingService:
```json
  "ConnectionStrings": {
    "LoggingService": "Server=localhost,1434;Database=BookStore_LoggingService;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true",
    "ProductService": "Server=localhost,1434;Database=BookStore_ProductService;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true",
    "AdministrationService": "Server=localhost,1434;Database=BookStore_Administration;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true",
    "SaasService": "Server=localhost,1434;Database=BookStore_Saas;User Id=sa;password=myPassw0rd;MultipleActiveResultSets=true"
  },
```

Under admin application, navigate to Products page and add/update products: 
![new-products](../../images/new-products.png)

Navigate to Administration -> Audit Logs to check the audit logs:
![product-logs](../../images/product-logs.png)
