# Microservice Startup Template: Database Migrations

> Running Sql-Server infrastructural service is required since default connection strings for microservices use Sql-Server running on container.

There are **two ways** to migrate databases and seed data for microservices. Either one of them or both of them can be used as long as data seeders are synced.

## Auto Migration (On-the-fly Migration)

> RabbitMq infrastructural service is also required for auto migration along with Sql-Server.

The aspect of auto migration is, *each microservice migrating its own database and seeding its own data*. To achieve this, each microservice checks pending migration and handles it on its own. 

Microservices has **DbMigrations** folder that contains *DatabaseMigrationChecker* and *DatabaseMigrationEventHandler* specific to that microservice. Since this behavior is shared across all the microservices, it has been defined as `PendingMigrationsCheckerBase` and `DatabaseMigrationEventHandlerBase` under shared **Hosting Microservices** project *DbMigrations* folder.

Microservices runs the *DatabaseMigrationChecker* at `OnPostApplicationInitialization` method.  If any pending migration exists, new `ApplyDatabaseMigrationsEto` event is published to [DistributedEventBus](https://docs.abp.io/en/abp/latest/Distributed-Event-Bus) to queue migration. 

```csharp
public virtual async Task CheckAsync()
{
    ...
    
    var pendingMigrations = await ServiceProvider
        .GetRequiredService<TDbContext>()
        .Database
        .GetPendingMigrationsAsync();

    if (pendingMigrations.Any())
    {
        await DistributedEventBus.PublishAsync(
            new ApplyDatabaseMigrationsEto
            {
                DatabaseName = DatabaseName
            }
        );
    }
    
    ...
}
```

Each migration handles its own database migration under `HandleEventAsync(ApplyDatabaseMigrationsEto eventData)` method of *DatabaseMigrationEventHandler*.

```csharp
public async Task HandleEventAsync(ApplyDatabaseMigrationsEto eventData)
{
    ...
        
    var schemaMigrated = await MigrateDatabaseSchemaAsync(eventData.TenantId);

    if (eventData.TenantId == null && schemaMigrated)
    {
        /* Migrate tenant databases after host migration */
        await QueueTenantMigrationsAsync();
    }

    ...
}
```

**Tenant Migrations** are also handled after host database migrations by queueing a new `ApplyDatabaseMigrationsEto` event passing *TenantId* argument.

```csharp
protected virtual async Task QueueTenantMigrationsAsync()
{
    var tenants = await TenantRepository.GetListWithSeparateConnectionStringAsync();
    foreach (var tenant in tenants)
    {
        await DistributedEventBus.PublishAsync(
            new ApplyDatabaseMigrationsEto
            {
                DatabaseName = DatabaseName,
                TenantId = tenant.Id
            }
        );
    }
}
```

This is handled under `MigrateDatabaseSchemaAsync(Guid? tenantId)` method of *DatabaseMigrationEventHandlerBase*.

```csharp
protected virtual async Task<bool> MigrateDatabaseSchemaAsync(Guid? tenantId)
{
    var result = false;

    using (CurrentTenant.Change(tenantId))
    {        
        ...
    }

    return result;
}
```

With this feature; newly created tenant with a specified connection string will have it's new database automatically created with all the tables and the initial seed data if available. Tenant management UI also has manually trigger for database creation & migration in case any problem occurs with automatic migration.

![apply-database-migrations](../../images/apply-database-migrations.png)

> If you don't want to use Auto Migration for any microservice, delete the `OnPostApplicationInitialization` method of the *HttpApiHost* module and microservice specific *DatabaseMigrationChecker* file along with related *DataSeeder*.

## Database Migrator

> If you want to use only Auto Migration (On-the-fly-Migration), it is safe to **delete this project** completely.

**DbMigrator** is a console application and used for database migrations and seeding data. It is located under the *shared* folder in the microservice template solution. `DbMigratorModule` depends on `SharedHostingModule` to use [mapped database configurations](infrastructure.md#hosting). Since it will be migrating the databases of microservices; it also depends on each microservice's `EntityFrameworkCoreModule` and `ApplicationContractsModule` modules.

DbMigratorService migrates the host first then tenants. 

```csharp
public async Task MigrateAsync(CancellationToken cancellationToken)
{
    await MigrateHostAsync(cancellationToken);
    await MigrateTenantsAsync(cancellationToken);
    _logger.LogInformation("Migration completed!");
}
```

`MigrateHostAsync` method migrates all the databases.

```csharp
private async Task MigrateAllDatabasesAsync(Guid? tenantId, CancellationToken cancellationToken)
{
   ...
       
   if (tenantId == null)
   {
      /* SaaS schema should only be available in the host side */
      await MigrateDatabaseAsync<SaasServiceDbContext>(cancellationToken);
   }

    await MigrateDatabaseAsync<AdministrationServiceDbContext>(cancellationToken);
    await MigrateDatabaseAsync<IdentityServiceDbContext>(cancellationToken);
    await MigrateDatabaseAsync<ProductServiceDbContext>(cancellationToken);

    await uow.CompleteAsync(cancellationToken);
    
	...
}
```

`MigrateTenantsAsync` method migrates each tenant based on their connection string

```csharp
private async Task MigrateTenantsAsync(CancellationToken cancellationToken)
{
    ...

    var tenants = await _tenantRepository.GetListAsync(includeDetails: true, cancellationToken: cancellationToken);
    var migratedDatabaseSchemas = new HashSet<string>();
    foreach (var tenant in tenants)
    {
        using (_currentTenant.Change(tenant.Id))
        {
            // Database schema migration
            var connectionString = tenant.FindDefaultConnectionString();
            if (!connectionString.IsNullOrWhiteSpace() && //tenant has a separate database
                !migratedDatabaseSchemas.Contains(connectionString)) //the database was not migrated yet
            {
                _logger.LogInformation($"Migrating tenant database: {tenant.Name} ({tenant.Id})");
                await MigrateAllDatabasesAsync(tenant.Id, cancellationToken);
                migratedDatabaseSchemas.AddIfNotContains(connectionString);
            }

            //Seed data
            ...
        }
	}
}
```

> If you decide to use **both DbMigrator and Auto Migration** approaches, you need to keep duplicate dataseeder files; one under your microservice DbMigrations folder for auto migration and other under shared DbMigrator project. You need to keep both of your data seeder files synchonized.

## IdentityService Data Seeding

IdentityService uses three different mapped [database configurations](infrastructure.md#hosting); *IdentityService*, *AdministrationService* and *SaasService* which are located under *appsettings.json* file. 

IdentityService seeding is **required** for AuthServer since it seeds the admin user/password (identity data) and initial identity server data (clients, api resources, scopes).

Both Auto Migration Data Seeding and DbMigrator Data Seeding uses `IdentityServerSeeder` and required *IdentityServerClients* used in the seeder is located in *appsettings.json* file

```json
"IdentityServerClients":{
  "MyProjectName_Web": {
    "RootUrl": "https://localhost:44321/"
  },
  "MyProjectName_Blazor": {
    "RootUrl": "https://localhost:44307/"
  },
  "MyProjectName_BlazorServer": {
    "RootUrl": "https://localhost:44314/"
  },
  "MyProjectName_PublicWeb": {
    "RootUrl": "https://localhost:44335/"
  },
  "MyProjectName_Angular": {
    "RootUrl": "http://localhost:4200"
  },
  "InternalGateway": {
    "RootUrl": "https://localhost:44302"
  },
  "WebGateway": {
    "RootUrl": "https://localhost:44325"
  },
  "PublicWebGateway": {
    "RootUrl": "https://localhost:44353"
  }
},
```

### Auto Migration Data Seeding

`IdentityServiceDatabaseMigrationEventHandler` is used for seeding *language management*, *identity server* and *identity* data.

```csharp
private async Task SeedDataAsync(Guid? tenantId, string adminEmail, string adminPassword)
{
    using (CurrentTenant.Change(tenantId))
    {
        if (tenantId == null)
        {
            await _languageManagementDataSeeder.SeedAsync();
            await _identityServerDataSeeder.SeedAsync();
        }

        await _identityDataSeeder.SeedAsync(
            adminEmail,
            adminPassword,
            tenantId
        );
    }
}
```

Data seeding occurs when **database is migrated**:

```csharp
public async Task HandleEventAsync(ApplyDatabaseMigrationsEto eventData)
{
    ...
    await SeedDataAsync(
        tenantId: eventData.TenantId,
        adminEmail: IdentityServiceDbProperties.DefaultAdminEmailAddress,
        adminPassword: IdentityServiceDbProperties.DefaultAdminPassword
    );
    ...
}
```

Or when a **tenant is created**:

```csharp
public async Task HandleEventAsync(TenantCreatedEto eventData)
{
    ...
    await SeedDataAsync(
        tenantId: eventData.Id,
        adminEmail: eventData.Properties.GetOrDefault(IdentityDataSeedContributor.AdminEmailPropertyName) ?? IdentityServiceDbProperties.DefaultAdminEmailAddress,
        adminPassword: eventData.Properties.GetOrDefault(IdentityDataSeedContributor.AdminPasswordPropertyName) ?? IdentityServiceDbProperties.DefaultAdminPassword
    );
    ...
}
```

Or when a tenants **connection string is updated**:

```csharp
public async Task HandleEventAsync(TenantConnectionStringUpdatedEto eventData)
{
    ...
    await SeedDataAsync(
        tenantId: eventData.Id,
        adminEmail: IdentityServiceDbProperties.DefaultAdminEmailAddress,
        adminPassword: IdentityServiceDbProperties.DefaultAdminPassword
    );
    ...
}
```

> Changing connection string will cause creation of a new database. This database will be empty if data from old database is not moved to new database. It is left for developer's choice.

### DbMigrator Data Seeding

**Host data** is seeded in `MigrateHostAsync` method:

```csharp
private async Task MigrateHostAsync(CancellationToken cancellationToken)
{
    _logger.LogInformation("Migrating Host side...");
    await MigrateAllDatabasesAsync(null, cancellationToken);
    await SeedDataAsync();
}
```

 `IdentityServerDataSeedContributor` is used for seeding the required `IdentityServerDataSeeder` and seeding data runs after migrating all the databases:

```csharp
private async Task SeedDataAsync()
{
    await _dataSeeder.SeedAsync(
        new DataSeedContext(_currentTenant.Id)
            .WithProperty(IdentityDataSeedContributor.AdminEmailPropertyName,
                IdentityServiceDbProperties.DefaultAdminEmailAddress)
            .WithProperty(IdentityDataSeedContributor.AdminPasswordPropertyName,
                IdentityServiceDbProperties.DefaultAdminPassword)
    );
}
```

**Tenant data** is seeded after host data is migrated in `MigrateTenantsAsync` method after tenant database migration is completed:

```csharp
private async Task MigrateTenantsAsync(CancellationToken cancellationToken)
{
    ...
        using (_currentTenant.Change(tenant.Id))
        {
            ...

            //Seed data
            _logger.LogInformation($"Seeding tenant data: {tenant.Name} ({tenant.Id})");
            await SeedDataAsync();
        }
    ...
}
```

> If you decide to use **both DbMigrator and Auto Migration** approaches, you need to keep both `IdentityServerDataSeeder` files located under *IdentityService/DbMigrations* and *DbMigrator* projects synchonized.

## AdministrationService Data Seeding

AdministrationService uses two different mapped [database configurations](infrastructure.md#hosting); *AdministrationService* and *SaasService* which are located under *appsettings.json* file. AdministrationService seeds the *initial admin permission* data.

### Auto Migration Data Seeding

`AdministrationServiceDatabaseMigrationEventHandler` is used for seeding permissions:

```csharp
private async Task SeedDataAsync(Guid? tenantId)
{
    using (CurrentTenant.Change(tenantId))
    {
        using (var uow = UnitOfWorkManager.Begin(requiresNew: true, isTransactional: true))
        {
            var multiTenancySide = tenantId == null
                ? MultiTenancySides.Host
                : MultiTenancySides.Tenant;

            var permissionNames = _permissionDefinitionManager
                .GetPermissions()
                .Where(p => p.MultiTenancySide.HasFlag(multiTenancySide))
                .Where(p => !p.Providers.Any() || p.Providers.Contains(RolePermissionValueProvider.ProviderName))
                .Select(p => p.Name)
                .ToArray();

            await _permissionDataSeeder.SeedAsync(
                RolePermissionValueProvider.ProviderName,
                "admin",
                permissionNames,
                tenantId
            );
        
            await uow.CompleteAsync();
        }
    }
}
```

Data seeding occurs when **database is migrated**:

```csharp
public async Task HandleEventAsync(ApplyDatabaseMigrationsEto eventData)
{
    ...
    await SeedDataAsync(eventData.TenantId);
	...        
}
```

Or when a **tenant is created**:

```csharp
public async Task HandleEventAsync(TenantCreatedEto eventData)
{
	...
    await SeedDataAsync(eventData.Id);
    ...
}
```

Or when a tenants **connection string is updated**:

```csharp
public async Task HandleEventAsync(TenantConnectionStringUpdatedEto eventData)
{
    ...
    await MigrateDatabaseSchemaAsync(eventData.Id);
    await SeedDataAsync(eventData.Id);
    ...
}
```

### DbMigrator Data Seeding

[IDataSeeder](https://docs.abp.io/en/abp/latest/Data-Seeding#idataseeder) is used for seeding the required data. 

## SaasService Data Seeding

SaasService uses two different mapped [database configurations](infrastructure.md#hosting); *SaasService* and *AdministrationService*  which are located under *appsettings.json* file. SaasService **does not seed any initial data**. If an initial saas data is required to be seeded, create a [SaasDataSeedContibutor](https://docs.abp.io/en/abp/latest/Data-Seeding#idataseedcontributor).

> SaasService database is only available for the host side.

### Auto Migration Data Seeding

`SaasServiceDatabaseMigrationEventHandler` is used for seeding initial saas data by calling `SeedAsync` method of newly created *SaasDataSeedContibutor* after migrating database schema.

```csharp
public async Task HandleEventAsync(ApplyDatabaseMigrationsEto eventData)
{
    ...
    await MigrateDatabaseSchemaAsync(null);
    // Seed data here
    ...
}
```

> SaasServiceDatabaseMigrationEventHandler only handles `ApplyDatabaseMigrationsEto` by default. 

### DbMigrator Data Seeding

[IDataSeeder](https://docs.abp.io/en/abp/latest/Data-Seeding#idataseeder) is used for seeding the required data and Data seed contributors are automatically discovered by the ABP Framework and executed as a part of the data seed process.

## ProductService Data Seeding

ProductService uses three different mapped [database configurations](infrastructure.md#hosting); *ProductService*, *AdministrationService*  and *SaasService* which are located under *appsettings.json* file. ProductService **does not seed any initial data**. If an initial product data is required to be seeded, create a [ProductDataSeedContibutor](https://docs.abp.io/en/abp/latest/Data-Seeding#idataseedcontributor).

### Auto Migration Data Seeding

`ProductServiceDatabaseMigrationEventHandler` is used for seeding initial product data by calling `SeedAsync` method of newly created *ProductDataSeedContibutor* after migrating database schema.

```csharp
public async Task HandleEventAsync(ApplyDatabaseMigrationsEto eventData)
{
    ...
    var schemaMigrated = await MigrateDatabaseSchemaAsync(eventData.TenantId);
    // Seed data here
    ...
}
```

If the product data needs to be seeded when a **tenant is created**:

```csharp
public async Task HandleEventAsync(TenantCreatedEto eventData)
{
    ...
    await MigrateDatabaseSchemaAsync(eventData.Id);
    // Seed data here
    ...
}
```

Or when a tenants **connection string is updated**:

```csharp
public async Task HandleEventAsync(TenantConnectionStringUpdatedEto eventData)
{
    ...
    await MigrateDatabaseSchemaAsync(eventData.Id);
    // Seed data here
    ...
}
```

### DbMigrator Data Seeding

Generic [IDataSeeder](https://docs.abp.io/en/abp/latest/Data-Seeding#idataseeder) is used to seed the required data and Data seed contributors are automatically discovered by the ABP Framework and executed as a part of the data seed process.
