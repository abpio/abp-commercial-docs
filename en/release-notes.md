# Release Notes / Change Logs

This document contains **brief release notes** for each release. Release notes only include **major features** and **visible enhancements**. They don't include all the development done in the related version.

## 4.3 (2021-04-23)

See the detailed [blog post / announcement](https://blog.abp.io/abp/ABP-Commercial-4.3-RC-Has-Been-Published) for the v4.3.

* New module: **CMS Kit (pro)**
* New module: **Forms**
* **Blazor Server Side** support
* **Extensibility** system for the Blazor UI
* A lot of improvements done to ripen the **Microservice Startup Template**, including "new service" template, automatic database migrations, solution structure improvements, Tye, Prometheus, Grafana integrations, and more
* Allow to use a **separate database schema** for tenants to not include host-related empty tables in tenant databases
* Creating & **migrating tenant databases on the fly**.
* **Enabling/disabling modules** per edition/tenant
* **Email settings** page
* **Required** navigation properties on Suite code generation

## 4.2 (2021-01-28)

See the detailed **[blog post / announcement](https://blog.abp.io/abp/ABP.IO-Platform-4-2-Final-Has-Been-Released)** for the v4.2.

* **Microservice startup template** (initial) to create microservice solutions.
* **Public website** application in the application startup template.
* **Blazor UI** for the Easy CRM sample application.
* Added login / **authorization** to the **Swagger UI** to test authorized APIs.
* **DBMS selection** on new application creation.
* Infrastructure for **Angular Unit Testing**.
* Iyzico integration for the **Payment** module.
* **Performance** optimization and other enhancements.

## 4.1 (2021-01-06)

See the detailed **[blog post / announcement](https://blog.abp.io/abp/ABP-IO-Platform-v4-1-Final-Has-Been-Released)** for the v4.1.

* **Organization Unit** Management for the Blazor UI.
* **Identity Server** Management for the Blazor UI.
* ABP Suite: **Navigation Property Selection** with Typeahead (supported by all UI types).
* **Spanish** language translation.

## 4.0 (2020-12-03)

See the detailed **[blog post / announcement](https://blog.abp.io/abp/ABP.IO-Platform-4.0-with-.NET-5.0-in-the-4th-Year)** for the v4.0.

* Upgraded to **.NET 5.0**.
* The **Blazor UI** option is now stable and officially supported.
* Completed the Blazor UI for the **file management** module.
* Upgraded to the **Identity Server 4.1.1** and revised the management UI.
* ABP Suite: Blazor UI **code generation**.
* ABP Suite: **Navigation property selection** supports dropdowns with auto-complete & lazy load.
* ABP Suite: **Generate new modules** inside an application solution.
* ABP Suite: Made the **backend code generation optional** to allow re-generate the UI with a different UI framework.

## 3.3 (2020-10-27)

See the detailed **[blog post / announcement](https://blog.abp.io/abp/ABP-Framework-ABP-Commercial-3.3-Final-Have-Been-Released)** for the v3.3.

* Completed fundamental features, modules and the theme integration for the **Blazor UI**.
* Multi-Tenant **social/external logins** with options configurable on runtime.
* **Linked Accounts** system to link multiple accounts and switch between them easily.
* **Paypal** & **Stripe** integrations for the Payment Module.
* **reCAPTCHA** option for login & register forms.
* **ABP Suite** improvements.

## 3.2 (2020-10-01)

### Blog Post

See the detailed **[blog post / announcement](https://blog.abp.io/abp/ABP-Framework-ABP-Commercial-3.2-RC-With-The-New-Blazor-UI)** for the v3.2.

### Major Features / Changes

* Released the preview (experimental) **Blazor UI** option.
* **Angular** UI for the [file management](https://commercial.abp.io/modules/Volo.FileManagement) module.
* Managing the **application features** for the **host** side.
* User **profile picture** for the account module.
* Options to enable, disable or force **two factor authentication** for tenants and users.

## 3.1 (2020-09-03)

### Blog Post

See the detailed **[blog post / announcement](https://blog.abp.io/abp/ABP-Framework-v3.1-Final-Has-Been-Released)** for the v3.1 release.

### Major Features / Changes

* Completely re-written the ABP Suite **Angular UI code generation**, using the Angular Schematics system.
* Implemented **Authorization Code Authentication Flow** for the Angular UI.
* Revised and documented **social/external logins** for the account module and tested with major providers.
* Introduced the new external login system supporting to login via **LDAP / Active Directory**. Also, added a setting page to configure the LDAP options.
* Created a new **security log system** and the user interface to save and report all the authentication related operations (login, logout, change password...) for users.
* Implemented **email & phone number verification**.
* Implementing **locking a user** for a given period of time (locked users can not login to the application).
* Added breadcrumb and file icons for the file management module.

## 3.0 (2020-07-01)

### Blog Post

See the detailed **[blog post / announcement](https://blog.abp.io/abp/ABP-Framework-v3.0-Has-Been-Released)** for the v3.0 release.

### Major Features

* New **File Management Module** that is used to store and manage files in your application.
* Migrated the Angular UI to the **Angular 10**.
* Published an **[API documentation](https://docs.abp.io/api-docs/commercial/2.9/api/index.html)** web site to explore the classes of the ABP Commercial.

## 2.9 (2020-06-04)

### Blog Post

See the detailed **[blog post / announcement](https://blog.abp.io/abp/ABP-Framework-v2.9.0-Has-Been-Released)** for the v2.9 release.

### Major Features / Enhancements

* New **Organization Unit** Management UI for the [Identity Module](https://commercial.abp.io/modules/Volo.Identity.Pro) to create hierarchical organization units and manage their members and roles.
* Created **Angular UI** for the [Chat Module](https://commercial.abp.io/modules/Volo.Chat).
* Implemented **Angular UI** for the [Easy CRM](https://docs.abp.io/en/commercial/latest/samples/easy-crm) application.
* [ABP Suite](https://commercial.abp.io/tools/suite) code generation support for **module development**.
* New [leptontheme.com](http://leptontheme.com/) web site to show the **[Lepton Theme](https://commercial.abp.io/themes) components**.

## 2.8 (2020-05-21)

### Blog Post

See the detailed **blog post / announcement** for the v2.8 release: [https://blog.abp.io/abp/ABP-v2.8.0-Releases-%26-Road-Map](https://blog.abp.io/abp/ABP-v2.8.0-Releases-%26-Road-Map)

This post also covers the [road map](road-map.md) and other news for the ABP.IO Platform.

### Major Features / Enhancements

* Completely renewed the **[Lepton Theme](https://commercial.abp.io/themes) styles** and add a new one.
* New module: Created a **real time [Chat Module](https://commercial.abp.io/modules/Volo.Chat)** that is built on ASP.NET Core SignalR. It currently has only the MVC / Razor Pages UI. Angular UI is on the way.
* Implemented **[module entity extension](guides/module-entity-extensions.md) system** for the **Angular UI**. Also improved the system to better handle float/double/decimal, date, datetime, enum and boolean properties.
* **Gravatar** integration for the Angular UI.
* Managing product groups on a **tree view** for the [EasyCRM sample application](samples/easy-crm.md).

## 2.7 (2020-05-07)

### Blog Post

See the detailed **blog post / announcement** for the v2.7 release:  https://blog.abp.io/abp/ABP-Framework-v2_7_0-Has-Been-Released 

### Major Features

* New module: **Text template management** (with angular and mvc UI - document is [coming](modules/text-template-management.md)).
* **Dynamically add properties** to current entities of the depended modules (see [module entity extensions](guides/module-entity-extensions.md))
* To be able to add **navigation properties** to entities with the ABP Suite (see [navigation properties](https://docs.abp.io/en/commercial/latest/abp-suite/generating-crud-page#navigation-properties))
* Dynamically add **data table columns** on the user interface (see the documents: [angular](ui/angular/data-table-column-extensions.md), [mvc](ui/aspnetcore/data-table-column-extensions.md))
* Created a rich **sample solution**, named "Easy CRM" (see the document)

### Other Enhancements

* Allow to dynamically **override the logo**.
* **Optimize database migrations** & seed code for multi-tenant multi-database systems.
* ABP Suite: Make **menu item active** on navigation menu when selected.
* ABP Suite: Improve **enum usage** while creating new entities.
* Bug fixes in the [Lepton Theme](https://commercial.abp.io/themes), [ABP Suite](https://commercial.abp.io/tools/suite) and  other modules.
