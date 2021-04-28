# Microservice Startup Template: Microservices

There are four microservices are included in the microservice startup template;

- **IdentityService** is located under *services/identity* folder. This service is an **infrastructural microservice** and hosts `Identity ` and `IdentityServer` related functionality and data store.
- **AdministrationService** is located under *services/administration* folder. This service is an **infrastructural microservice** and hosts administrative functionality such as `permission-management`, `setting-management`, `language-management`, `audit-logging` and such. 
- **SaasService** is located under *services/saas* folder. This service is an **infrastructural microservice** and hosts multi-tenancy related functionality and data store.
- **ProductService** is located under *services/product* folder. This is a **sample microservice** to examine and take as a reference for your services.

The infrastructural microservices typically use the [pre-built modules](../../modules/index.md).

The microservices are highlighted in the overall solution diagram below:

![overall-applications](../../images/overall-microservices.gif)

All microservices;

* Have their own solutions (`.sln` files) including test projects under their respected folders. 

- Depend on `SharedHostingMicroservicesModule` which contains configuration helpers for **JwtBearer** authorization, **Swagger** and **DbMigrations**,
- Contains configurations for AuthServer, Redis, RabbitMQ, ElasticSearch in *appsettings.json* file,
- Are capable of **on-the-fly database migration**; have `DbMigrations` folder that contains `MigrationChecker` and `MigrationEventHandler`. This allows microservices migrate and seed their databases using distributed event bus as an alternative usage to shared DbMigrator,
- Use **Sql Server**, containing `.EntityFrameworkCore` layer. This layer contains related database contexts, database context factories and migrations along with related module db configurations

> If you want to switch your database provider to **MongoDb** instead of EntityFrameworkCore for any of the microservice, you need to create `.MongoDb` project instead of `.EntityFrameworkCore`. Afterwards, add related modules MongoDb packages dependencies along with similar configurations made in EntityFrameworkCore layer like db context replacement and such. For more, check [MongoDB integration](https://docs.abp.io/en/abp/latest/Best-Practices/MongoDB-Integration).

## IdentityService

IdentityService is an infrastructural microservice that provides modular solution by combining  the Identity and Identity-Server modules. All the related layers references respected layers of these modules.

### Identity-Server Authorization

IdentityService is defined as api resource and api scope with the name **IdentityService** in `IdentityServerDataSeeder`.

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

```csharp
private async Task CreateApiScopesAsync()
{
    await CreateApiScopeAsync("IdentityService");
    await CreateApiScopeAsync("AdministrationService");
    await CreateApiScopeAsync("SaasService");
    await CreateApiScopeAsync("ProductService");
}
```

See [IdentityService Data Seeding](database-migrations.md#identity-service-data-seeding) for seeding options.

In order make make authorized requests to IdentityService by applications/microservices, **IdentityService** scope must have been granted for that client.

Web application (Mvc/Angular/Blazor) clients are allowed to request **IdentityService** scope when created:

```csharp
private async Task CreateClientsAsync()
{
    ...        
    scopes: commonScopes.Union(new[]
    {
    	"IdentityService",
    	"AdministrationService",
		"SaasService",
    	"ProductService"
    }),
    ...
}
```

Web Gateway Swagger and Internal Gateway Swagger clients are allowed to request **IdentityService** scope when created:

```csharp
private async Task CreateSwaggerClientsAsync()
{
    await CreateSwaggerClientAsync("InternalGateway", new []{ "IdentityService", "AdministrationService", "SaasService", "ProductService"});
    await CreateSwaggerClientAsync("WebGateway", new []{ "IdentityService", "AdministrationService", "SaasService", "ProductService"};
	...
}
```

AdministrationService client is allowed to request **IdentityService** scope when created:

```csharp
private async Task CreateClientsAsync()
{
    ...
    //Administration Service Client
    await CreateClientAsync(
        name: "MyProjectName_AdministrationService",
        scopes: commonScopes.Union(new[]
        {
            "IdentityService"
        }),
        grantTypes: new[] {"client_credentials"},
        secret: "1q2w3e*".Sha256(),
        permissions: new[] {IdentityPermissions.Users.Default}
    );
}
```

### Database Configuration

**IdentityServiceDbContext** implements 

- `IIdentityDbContext` 
- `IIdentityServerDbContext` 

in order to use these module db contexts as combined single db context. However DI container must also dynamically inject **IdentityServiceDbContext** whenever `IIdentityDbContext` or `IIdentityServerDbContext` is requested. The configuration under `ConfigureServices` provides the runtime database replacement:

```csharp
context.Services.AddAbpDbContext<IdentityServiceDbContext>(options =>
{
    options.ReplaceDbContext<IIdentityDbContext>();
    options.ReplaceDbContext<IIdentityServerDbContext>();
    ...
});
```

There is also a SQL configuration for changing the migration table name to separate each service migration history table:

```csharp
Configure<AbpDbContextOptions>(options =>
{
    options.Configure<IdentityServiceDbContext>(c =>
    {
        c.UseSqlServer(b =>
        {
            b.MigrationsHistoryTable("__IdentityService_Migrations");
        });
    });
});
```

## AdministrationService

AdministrationService is an infrastructural microservice that hosts [Permission Management Module](https://docs.abp.io/en/abp/latest/Modules/Permission-Management), [Feature Management Module](https://docs.abp.io/en/abp/latest/Modules/Feature-Management), [Setting Management Module](https://docs.abp.io/en/abp/latest/Modules/Setting-Management), [Audit Logging](https://docs.abp.io/en/commercial/latest/modules/audit-logging), [Language Management Module](https://docs.abp.io/en/commercial/latest/modules/language-management), [Text Template Management Module](https://docs.abp.io/en/commercial/latest/modules/text-template-management), [Lepton Theme Module](https://docs.abp.io/en/commercial/latest/themes/lepton) and functioning as combination of these modules. All the related layers references respected layers of these modules. 

### Identity-Server Authorization

As default; Web application (Mvc/Angular/Blazor), Public Web application, Web Gateway Swagger and Internal Gateway Swagger clients are granted to make requests to **AdministrationService** scope. The definition of this api resource and scope is done in `IdentityServerDataSeeder`.

AdministrationService has [synched interservice-communication](TODO) with **IdentityService** and should be able to make synched http requests to IdentityService order to function properly. This is done by adding this service as a client using `client_credentials` grant type with name *MyProjectName_AdministrationService* requesting *IdentityService* with `IdentityPermissions.Users.Default` permission. This is used for administration service requesting the user list in administration pages.

```csharp
//Administration Service Client
await CreateClientAsync(
    name: "MyProjectName_AdministrationService",
    scopes: commonScopes.Union(new[]
    {
        "IdentityService"
    }),
    grantTypes: new[] {"client_credentials"},
    secret: "1q2w3e*".Sha256(),
    permissions: new[] {IdentityPermissions.Users.Default}
);
```

To make the *client_credential* requests, IdentityModel configuration is done under `IdentityClients` section of appsettings along with `RemoteServices` configuration which points to **Internal Gateway** to locate IdentityService endpoint. This is done by using [Dynamic C# API Client Proxies](https://docs.abp.io/en/abp/latest/API/Dynamic-CSharp-API-Clients).

> If you need to make requests from AdministrationService to other microservices, you need to add their scope to client creation with required permission as in IdentityService user list request.

### Database Configuration

**AdministrationServiceDbContext** implements

- `IPermissionManagementDbContext`
- `ISettingManagementDbContext`
- `IFeatureManagementDbContext`
- `IAuditLoggingDbContext`
- `ILanguageManagementDbContext`
- `ITextTemplateManagementDbContext`
- `IBlobStoringDbContext`

in order to use these module db contexts as combined single db context. DI container dynamically injects **AdministrationServiceDbContext** whenever one of the used modules dbcontext is requested. The configuration under `ConfigureServices` provides the runtime database replacement

```csharp
context.Services.AddAbpDbContext<AdministrationServiceDbContext>(options =>
{
    options.ReplaceDbContext<IPermissionManagementDbContext>();
    options.ReplaceDbContext<ISettingManagementDbContext>();
    options.ReplaceDbContext<IFeatureManagementDbContext>();
    options.ReplaceDbContext<IAuditLoggingDbContext>();
    options.ReplaceDbContext<ILanguageManagementDbContext>();
    options.ReplaceDbContext<ITextTemplateManagementDbContext>();
    options.ReplaceDbContext<IBlobStoringDbContext>();
    ...
});
```

There is also a SQL configuration for changing the migration table name to separate each service migration history table

```csharp
Configure<AbpDbContextOptions>(options =>
{
    options.Configure<AdministrationServiceDbContext>(c =>
    {
        c.UseSqlServer(b =>
        {
            b.MigrationsHistoryTable("__AdministrationService_Migrations");
        });
    });
});
```

## SaasService

SaasService is an infrastructural microservice that for multi-tenancy functionality and data store.

### Identity-Server Authorization

In order make make authorized requests to SaasService by applications/microservices, **SaasService scope** must have been granted for that client.

As default; Web applications (Mvc/Angular/Blazor), Web Gateway Swagger and Internal Gateway Swagger clients are granted to make requests to **SaasService** scope. The definition of this api resource and scope is done in `IdentityServerDataSeeder`.

### Database Configuration

**SaasServiceDbContext** implements

- `ISaasDbContext`

in order to use this module db context. DI container dynamically injects **SaasServiceDbContext** whenever the Saas module db context is requested. The configuration under `ConfigureServices` provides the runtime database replacement

```csharp
context.Services.AddAbpDbContext<SaasServiceDbContext>(options =>
{
    options.ReplaceDbContext<ISaasDbContext>();
    ...
});
```

There is also a SQL configuration for changing the migration table name to separate each service migration history table

```csharp
Configure<AbpDbContextOptions>(options =>
{
    options.Configure<SaasServiceDbContext>(c =>
    {
        c.UseSqlServer(b =>
        {
            b.MigrationsHistoryTable("__SaasService_Migrations");
        });
    });
});
```

## ProductService

ProductService is a sample microservice for examination, created with using [Module Development Best Practices & Conventions](https://docs.abp.io/en/abp/latest/Best-Practices/Index). Since Microservice template solution is created with modularity in mind, it is pretty straight forward to integrate modules as microservices into the solution.

ProductService [solution structure](https://docs.abp.io/en/commercial/latest/startup-templates/module/solution-structure) contains *.Web* layer as **library** instead of traditional running project which provides modular UI development. When you develop the UI of your application inside the module, it will be rendered in your host application.

### Identity-Server Authorization

As default, Web applications (Mvc/Angular/Blazor), Public Web application and all the Gateway Swagger clients are granted to make requests to **ProductService** scope. The definition of this api resource and scope is done in `IdentityServerDataSeeder`.

### Database Configuration

**ProductService.EntityFrameworkCore** project contains `ProductServiceDbContext`, `ProductServiceDbContextFactory`, `EfCoreEntityExtensionsMapping`,  `Migrations`, EfCore configuration and ProductRepository implementation. **ProductServiceDbContext** implements

- `IProductServiceDbContext`

which contains ProductService specific `DbSets`. Moreover, there is no need for runtime db context replacement hence there is no configuration related with it.

There is a SQL configuration for changing the migration table name to separate each service migration history table

```csharp
Configure<AbpDbContextOptions>(options =>
{
    options.Configure<ProductServiceDbContext>(c =>
    {
        c.UseSqlServer(b =>
        {
            b.MigrationsHistoryTable("__ProductService_Migrations");
        });
    });
});
```

## Next

- [Microservice Startup Template: Gateways](gateways.md)