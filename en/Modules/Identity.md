# Identity Module

This module implements the User and Role system of an application;

* Built on the [Microsoft's ASP.NET Core Identity](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity) library.
* Manage **roles** and **users** in the system. A user is allowed to have **multiple roles**.
* Set **permissions** in role and user levels.
* Enable/disable **two factor authentication** and user **lockout** per user.
* Manage basic **user profile** and **password**.
* Manage **claim types** in the system, set claims to roles and users.
* Setting page to manage **password complexity**, user sign-in, account and lockout.

See [the module description page](https://commercial.abp.io/modules/Volo.Identity.Pro) for an overview of the module features.

## How to Install

Identity is pre-installed in [the startup templates](../Startup-Templates/Index). So, no need to manually install it.

## Internals

### Domain Layer

#### Aggregates

This module follows the [Entity Best Practices & Conventions](https://docs.abp.io/en/abp/latest/Best-Practices/Entities) guide.

##### User

A user is generally a person logins to and uses the application.

* `IdentityUser` (aggregate root): Represents a user in the system.
  * `IdentityUserRole` (collection): Roles to the user.
  * `IdentityUserClaim` (collection): Custom claims of the user.
  * `IdentityUserLogin` (collection): External logins of the user.
  * `IdentityUserToken` (collection): Tokens of the user (used by the Microsoft Identity services).

##### Role

A role is typically a group of permissions to assign to the users.

* `IdentityRole` (aggregate root): Represents a role in the system.
  * `IdentityRoleClaim` (collection): Custom claims of the role.

##### Claim Type

A claim type is a definition of a custom claim that can be assigned to other entities (like roles and users) in the system.

* `IdentityClaimType` (aggregate root): Represents a claim type definition. It contains some properties (e.g. Required, Regex, Description, ValueType) to define the claim type and the validation rules.

#### Repositories

This module follows the [Repository Best Practices & Conventions](https://docs.abp.io/en/abp/latest/Best-Practices/Repositories) guide.

Following custom repositories are defined for this module:

* `IIdentityUserRepository`
* `IIdentityRoleRepository`
* `IIdentityClaimTypeRepository`

#### Domain Services

This module follows the [Domain Services Best Practices & Conventions]( https://docs.abp.io/en/abp/latest/Best-Practices/Domain-Services) guide.

##### User Manager

`IdentityUserManager` is used to manage users, their roles, claims, passwords, emails, etc. It is derived from Microsoft Identity's `UserManager<T>` class where `T` is `IdentityUser`.

##### Role Manager

`IdentityRoleManager` is used to manage roles and their claims. It is derived from Microsoft Identity's `RoleManager<T>` class where `T` is `IdentityRole`.

##### Claim Type Manager

`IdenityClaimTypeManager` is used to perform some operations for the `IdentityClaimType` aggregate root.

### Application Layer

### Database Providers

#### Common

##### Table/Collection Prefix & Schema

All tables/collections use the `Abp` prefix by default. Set static properties on the `AbpIdentityDbProperties` class if you need to change the table prefix or set a schema name (if supported by your database provider).

##### Connection String

This module uses `AbpIdentity` for the connection string name. If you don't define a connection string with this name, it fallbacks to the `Default` connection string.

See the [connection strings](https://docs.abp.io/en/abp/latest/Connection-Strings) documentation for details.

#### Entity Framework Core

##### Tables

* **AbpRoles**
  * AbpRoleClaims
* **AbpUsers**
  * AbpUserClaims
  * AbpUserLogins
  * AbpUserRoles
  * AbpUserTokens
* **AbpClaimTypes**

#### MongoDB

##### Collections

* **AbpRoles**
* **AbpUsers**
* **AbpClaimTypes**











## Menu Items

Identity module adds the following items to the "Main" menu, under the "Administration" menu item:

* Roles: Role management page.
* Users: User management page.
* Claim Types: Claim type management page.

## Database Tables/Collections

Identity module adds some tables/collections to the database, based on the database provider you're using.

* 

## Permissions

See the `IdentityPermissions` class members for all permissions defined for this module.

## Settings

See the `IdentitySettingNames` class members for all settings defined for this module.

## Packages

This module follows the [module development best practices guide](https://docs.abp.io/en/abp/latest/Best-Practices/Index) and consists of several NuGet and NPM packages. See the guide if you want to understand the packages and relations between them.

### NuGet Packages

* Volo.Abp.Identity.Domain.Shared
* Volo.Abp.Identity.Domain
* Volo.Abp.Identity.Pro.Application.Contracts
* Volo.Abp.Identity.Pro.Application
* Volo.Abp.Identity.EntityFrameworkCore
* Volo.Abp.Identity.MongoDB
* Volo.Abp.Identity.AspNetCore
* Volo.Abp.PermissionManagement.Domain.Identity
* Volo.Abp.Identity.Pro.HttpApi
* Volo.Abp.Identity.Pro.HttpApi.Client
* Volo.Abp.Identity.Pro.Web

### NPM Packages

* @volo/abp.ng.identity
* @volo/abp.ng.identity.config

## Data Seed

This module adds some initial data (see [the data seed system](https://docs.abp.io/en/abp/latest/Data-Seeding)) to the database when you run the `.DbMigrator` application:

* Creates an `admin` role with all the permissions granted.
* Creates an `admin` user with the `admin` role and `1q2w3E*` as the password.

You normally change this password when you first run the application in your production environment. But if you want to change the password of the seed data, find the *ProjectName*DbMigrationService in your solution, locate to the `MigrateAsync` method. There will be a line like that:

````csharp
await _dataSeeder.SeedAsync();
````

Change it like that:

````csharp
await _dataSeeder.SeedAsync(
    new DataSeedContext()
        .WithProperty("AdminPassword", "myPassW00rd42")
);
````

Just like the password, you can also set the admin email (use the `AdminEmail` key in this case).

> The [data seed contributor](https://docs.abp.io/en/abp/latest/Data-Seeding) class of the Identity module is `IdentityDataSeedContributor` which internally uses the `IIdentityDataSeeder` service.

## Distributed Events

This module doesn't define any additional distributed event. See the [standard distributed events](https://docs.abp.io/en/abp/latest/Distributed-Event-Bus).