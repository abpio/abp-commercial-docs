# Release Notes / Change Logs

This document contains **brief release notes** for each release. Release notes only include major features and visible enhancements. They not don't include all the development done in the related version.

## 2.7.0

### Blog Post

See the detailed **blog post / announcement** for the v2.7.0 release:  https://blog.abp.io/abp/ABP-Framework-v2_7_0-Has-Been-Released 

### Major features

* New module: **Text template management** (with angular and mvc UI - document is [coming](modules/text-template-management.md)).
* **Dynamically add properties** to current entities of the depended modules (see [the document](guides/module-entity-extensions.md))
* Adding **navigation properties** to entities on the ABP Suite.
* Dynamically add **data table columns** on the user interface (see the documents: [angular](ui/angular/data-table-column-extensions.md), [mvc](ui/aspnetcore/data-table-column-extensions.md))
* Created a rich **sample solution**, named "Easy CRM" (see the document)

### Other Enhancements

* Allow to dynamically **override the logo**.
* **Optimize database migrations** & seed code for multi-tenant multi-database systems.
* ABP Suite: Make **menu item active** on navigation menu when selected.
* ABP Suite: Improve **enum usage** while creating new entities.
* Improvements on the [Lepton Theme](https://commercial.abp.io/themes), [ABP Suite](https://commercial.abp.io/tools/suite) and  other modules.

### What's Coming Next?

Beside the completed features, the following features are currently being developed and will be released in the next versions:

* **Real-time chat** module built with the ASP.NET Core SignalR.
* **Organization units** system for the [identity module](modules/identity.md) to create hierarchical organization units and manage their members and roles.
