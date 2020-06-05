# Release Notes / Change Logs

This document contains **brief release notes** for each release. Release notes only include major features and visible enhancements. They not don't include all the development done in the related version.

## 2.9.0 (2020-06-04)

### Blog Post

See the detailed **[blog post / announcement](https://blog.abp.io/abp/ABP-Framework-v2.9.0-Has-Been-Released)** for the v2.9.0 release.

### Major Features / Enhancements

* New **Organization Unit** Management UI for the [Identity Module](https://commercial.abp.io/modules/Volo.Identity.Pro) to create hierarchical organization units and manage their members and roles.
* Created **Angular UI** for the [Chat Module](https://commercial.abp.io/modules/Volo.Chat).
* Implemented **Angular UI** for the [Easy CRM](https://docs.abp.io/en/commercial/latest/samples/easy-crm) application.
* [ABP Suite](https://commercial.abp.io/tools/suite) code generation support for **module development**.
* New [leptontheme.com](http://leptontheme.com/) web site to show the **[Lepton Theme](https://commercial.abp.io/themes) components**.

## 2.8.0 (2020-05-21)

### Blog Post

See the detailed **blog post / announcement** for the v2.8.0 release: [https://blog.abp.io/abp/ABP-v2.8.0-Releases-%26-Road-Map](https://blog.abp.io/abp/ABP-v2.8.0-Releases-%26-Road-Map)

This post also covers the [road map](road-map.md) and other news for the ABP.IO Platform.

### Major Features / Enhancements

* Completely renewed the **[Lepton Theme](https://commercial.abp.io/themes) styles** and add a new one.
* New module: Created a **real time [Chat Module](https://commercial.abp.io/modules/Volo.Chat)** that is built on ASP.NET Core SignalR. It currently has only the MVC / Razor Pages UI. Angular UI is on the way.
* Implemented **[module entity extension](guides/module-entity-extensions.md) system** for the **Angular UI**. Also improved the system to better handle float/double/decimal, date, datetime, enum and boolean properties.
* **Gravatar** integration for the Angular UI.
* Managing product groups on a **tree view** for the [EasyCRM sample application](samples/easy-crm.md).

## 2.7.0 (2020-05-07)

### Blog Post

See the detailed **blog post / announcement** for the v2.7.0 release:  https://blog.abp.io/abp/ABP-Framework-v2_7_0-Has-Been-Released 

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
