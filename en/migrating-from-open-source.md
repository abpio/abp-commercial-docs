# Migrating From ABP Framework

This guide provides you a step-by-step guidance to migrating your existing application (that uses the ABP Framework) to ABP Commercial. Since, ABP Commercial uses the main structure of the ABP Framework and built top of that, this process is pretty straight-forward, you can apply the steps mentioned in the each steps and easily migrate your project to ABP Commercial.

> After following this documentation, you should be able to migrate your project to ABP Commercial. However, if you had any problems or could not migrate your project, we are providing paid consultancy, which you can find details at [https://commercial.abp.io/additional-services](https://commercial.abp.io/additional-services). In this page, you can find related informations about our trainings, custom project development and porting existing projects services, and you can fill-out the contact form, so we can reach out to you!

## ABP Commercial Migration Steps

> In this guide, we assume that you have a middle-complex ABP based solution and want to migrate to ABP Commercial. Throuhgout this documentation, `Acme.BookStore` application will be used as a reference solution (example application that described in ABP's tutorial documents), which you can find at [https://github.com/abpframework/abp-samples/tree/master/BookStore-Mvc-EfCore](https://github.com/abpframework/abp-samples/tree/master/BookStore-Mvc-EfCore) but all of these steps are applicable for your own applications, only some of them can be changed according to your project choose and structure. However, the migration flow is the same.

There are 4 main steps to migrating from ABP Framework to ABP Commercial, and each one of them are explained in the following sections, step-by-step and for project based:

### 1. License Transition

First step is to obtaining the necessary license for ABP Commercial to be able to get benefit of the pro modules and unlock the additional features. To do that, you should first get your `AbpLicenseCode` and `ApiKey` from the [organization's detail page](https://commercial.abp.io/my-organizations).

Then, you can update the **NuGet.Config** file in the root directory of your solution and add the *packageSource* as follows (don't forget to replace `<api-key>` placeholder): 

```diff
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="nuget.org" value="https://api.nuget.org/v3/index.json" />
+   <add key="ABP Commercial NuGet Source" value="https://nuget.abp.io/<api-key>/v3/index.json" />
  </packageSources>
</configuration>
```

After that, you can open the `appsettings.json` files under the `*.DbMigrator` and `*.Domain` projects and add your `AbpLicenseCode`:

```json
{
    //...
    
    "AbpLicenseCode": "<AbpLicenseCode>"
}

```

> `ApiKey` is needed to be able to use ABP Commercial's NuGet packages and `AbpLicenseCode` is needed for license check per module.

### 2. Installing the ABP Commercial Modules

After, you have added the `ApiKey` and `AbpLicenseCode` to the relevant places, now you can add [ABP Commercial's modules](modules/index.md) to your solution. ABP Commercial provides plent of modules that extend the ABP Framework modules, such as `Account PRO` module over `Account` module or `Identity Pro` module over `Identity` module. 

To replace these modules and also add the additional modules provided by ABP Commercial, you can use the `abp add-module` command (and then remove the free modules in the next section). This command finds all packages of the specified module, finds the related projects in the solution and adds each packages into the corresponding project in the solution. Therefore, by using this command, you don't need to manually add the package references to the `*.csproj` files and add related `[DependsOn(typeof(<>))]` statements to the module classes, instead this command do this behalf of you.

You can run the following commands one after another in your solution directory and add all the related modules into your solution, like you have started with [one of the startup templates of ABP Commercial](startup-templates/index.md):

1. `abp add-module Volo.Identity.Pro --skip-db-migrations` → [Identity Module](modules/identity.md)
2. `abp add-module Volo.OpenIddict.Pro --skip-db-migrations` → [OpenIddict Module](modules/openiddict.md)
3. `abp add-module Volo.Saas --skip-db-migrations` → [SaaS Module](modules/saas.md)
4. `abp add-module Volo.AuditLogging.Ui --skip-db-migrations` → [Audit Logging UI Module](modules/audit-logging.md)
5. `abp add-module Volo.Account.Pro --skip-db-migrations` → [Account Module](modules/account.md)
6. `abp add-module Volo.TextTemplateManagement --skip-db-migrations` → [Text Template Management Module](modules/text-template-management.md)
7. `abp add-module Volo.LanguageManagement --skip-db-migrations` → [Language Management Module](modules/language-management.md)
8. `abp add-module Volo.Gdpr --skip-db-migrations` → [GDPR Module](modules/gdpr.md)
9. `abp add-module Volo.Abp.BlobStoring.Database --skip-db-migrations` → [Blob Storing - Database Provider](https://docs.abp.io/en/abp/latest/Blob-Storing-Database)

> These 9 modules are pre-installed to the [startup templates of ABP Commercial](startup-templates/index.md). Therefore, you can install all of them if you want to align your project with the startup templates, but it's totally optional, so if you you can skip running the command above for a module that you don't want to add into your solution.

After running the commands above, all of the related commercial packages and their dependencies will be added into your solution. In addition to these module packages, you can add `Volo.Abp.Commercial.SuiteTemplates` package into our domain application to be able to use ABP Suite later on. By doing that you will be able to add your solution from [ABP Suite UI](abp-suite/index.md) and generate CRUD pages for your applications whenever you want. 

So, open your `*Domain.csproj` file and add the line below (don't forget to replace the `<Version>` placeholder):

```xml
<PackageReference Include="Volo.Abp.Commercial.SuiteTemplates" Version="<Version>" />
```

Then, for the final step, you need to add the related `DependsOn` statement to the `*DomainModule.cs` file as follows:

```cs
[DependsOn(typeof(VoloAbpCommercialSuiteTemplatesModule))]
public class BookStoreDomainModule : AbpModule
{
    //omited for code abbreviation...
}
```

### 3. Removing the ABP Framework Module References & Updating Configurations

After the license transition and installing the ABP Commercial Modules, now you can remove the unnecessary free modules. For example, now you don't need the `Identity` module in your solution, because you have added the `Identity PRO` module in the previous section and it's already has dependcy to the free module and extends it.

You should remove various dependencies and references in different projects in your solution. All of the required changes are listed below in different sections, please apply the following steps to remove the unnecessary ABP Framework Modules:

#### 3.1 - Domain.Shared Project

`*Domain.Shared.csproj`:

```diff
-    <PackageReference Include="Volo.Abp.Identity.Domain.Shared" Version="8.0.4" />
-    <PackageReference Include="Volo.Abp.TenantManagement.Domain.Shared" Version="8.0.4" />
-    <PackageReference Include="Volo.Abp.OpenIddict.Domain.Shared" Version="8.0.4" />  
```

`*DomainSharedModule.cs`:

```diff
- using Volo.Abp.TenantManagement;

-    typeof(AbpIdentityDomainSharedModule),
-    typeof(AbpOpenIddictDomainSharedModule),
-    typeof(AbpTenantManagementDomainSharedModule)  
```

#### 3.2 - Domain Project

`*Domain.csproj`:

```diff
-    <PackageReference Include="Volo.Abp.Identity.Domain" Version="8.0.4" />
-    <PackageReference Include="Volo.Abp.TenantManagement.Domain" Version="8.0.4" />
-    <PackageReference Include="Volo.Abp.OpenIddict.Domain" Version="8.0.4" />  
```

`*DomainModule.cs`:

```diff
- using Volo.Abp.TenantManagement;

-    typeof(AbpIdentityDomainModule),
-    typeof(AbpOpenIddictDomainModule),
-    typeof(AbpTenantManagementDomainModule),
```

After removing the unnecessary references, we should update the namespaces in the `BookStoreDbMigrationService` class under the **Data** folder:

```diff
- using Volo.Abp.TenantManagement;
+ using Volo.Saas.Tenants;
```

#### 3.3 - EntityFrameworkCore Project

`*EntityFrameworkCore.csproj`:

```diff
-    <PackageReference Include="Volo.Abp.Identity.EntityFrameworkCore" Version="8.0.4" />
-    <PackageReference Include="Volo.Abp.TenantManagement.EntityFrameworkCore" Version="8.0.4" />
-    <PackageReference Include="Volo.Abp.OpenIddict.EntityFrameworkCore" Version="8.0.4" />  
```

`*EntityFrameworkCoreModule.cs`:

```diff
- using Volo.Abp.TenantManagement.EntityFrameworkCore;

-    typeof(AbpIdentityEntityFrameworkCoreModule),
-    typeof(AbpOpenIddictEntityFrameworkCoreModule),
-    typeof(AbpTenantManagementEntityFrameworkCoreModule)  
```

`*DbContext.cs`:

```diff
- using Volo.Abp.TenantManagement;
- using Volo.Abp.TenantManagement.EntityFrameworkCore;
+ using Volo.Saas.Editions;
+ using Volo.Saas.EntityFrameworkCore;
+ using Volo.Saas.Tenants;

[ReplaceDbContext(typeof(IIdentityDbContext))]
- [ReplaceDbContext(typeof(ITenantManagementDbContext))]
+ [ReplaceDbContext(typeof(ISaasDbContext))]
[ConnectionStringName("Default")]
public class BookStoreDbContext :
    AbpDbContext<BookStoreDbContext>,
    IIdentityDbContext,
-   ITenantManagementDbContext
+   ISaasDbContext
{
    //...

-    // Tenant Management
-    public DbSet<Tenant> Tenants { get; set; }
-    public DbSet<TenantConnectionString> TenantConnectionStrings { get; set; }

+    // SaaS
+    public DbSet<Tenant> Tenants { get; set; }
+    public DbSet<Edition> Editions { get; set; }
+    public DbSet<TenantConnectionString> TenantConnectionStrings { get; set; }

    //...

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);

        //...

-        builder.ConfigureIdentity();
+        builder.ConfigureIdentityPro();
-        builder.ConfigureOpenIddict();
+        builder.ConfigureOpenIddictPro();
-        builder.ConfigureTenantManagement();
+        builder.ConfigureSaas();

    }
}
```

#### 3.4 - Application.Contracts Project

`*Application.Contracts.csproj`:

```diff
-    <PackageReference Include="Volo.Abp.Account.Application.Contracts" Version="8.0.4" />
-    <PackageReference Include="Volo.Abp.Identity.Application.Contracts" Version="8.0.4" />
-    <PackageReference Include="Volo.Abp.TenantManagement.Application.Contracts" Version="8.0.4" />
```

`*ApplicationContractsModule.cs`:

```diff
- using Volo.Abp.TenantManagement;

-    typeof(AbpAccountApplicationContractsModule),
-    typeof(AbpTenantManagementApplicationContractsModule),
```

#### 3.5 - Application Project
#### 3.6 - HttpApi Project
#### 3.7 - HttpApi.Client Project
#### 3.8 - Web Project

### 4. Creating Migrations & Running Application

//TODO: creating migration, applying to the db and running the application

## Consultancy

If you find the migration process challenging or prefer professional assistance, we offer a [paid consultancy service](https://commercial.abp.io/additional-services). Our experienced consultants can help ensure a smooth transition to ABP Commercial, addressing any specific needs or challenges your project may encounter. For detailed guidance and support, feel free to [reach out](https://commercial.abp.io/contact)!