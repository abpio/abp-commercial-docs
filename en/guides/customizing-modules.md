# Customizing the Modules

This guide has references to related documents that explain **how to customize a depended [application module](../modules/index.md)** when you want to use the module as **NuGet/NPM package references**.

## ABP Framework Infrastructure

ABP Framework already provides many different approaches to **extend or customize** a depended module. See the "**[Customizing the Application Modules](https://docs.abp.io/en/abp/latest/Customizing-Application-Modules-Guide)**" guide as the **fundamental reference**.

## ABP Commercial Infrastructure

ABP Commercial adds **more extension points** on top of the ABP Framework.

> This section **only focuses on the additional features** provided by the ABP Commercial. You should definitely see the guide mentioned in the previous section.

### Module Entity Extension System

Module entity extension system is the **main extension system** that allows you to **define new properties** for existing entities of the depended modules. It automatically **adds properties to the entity, database, HTTP API and the user interface** in a single point.

* See the [Module Entity Extensions document](module-entity-extensions.md) to learn how to use it.

### Other Extension Points

#### Entity Actions

Entity action extension system allows you to add a new action to the action menu for an entity on the user interface. See the related documents to learn how to use this system:

* [Entity Action Extensions for ASP.NET Core UI](../ui/aspnetcore/entity-action-extensions.md)
* [Entity Action Extensions for Angular](../ui/angular/entity-action-extensions.md)

#### Data Table Column Extensions

Data table column extension system allows you to add a new column in the data table on the user interface. See the related documents to learn how to use this system:

* [Data Table Column Extensions for ASP.NET Core UI](../ui/aspnetcore/data-table-column-extensions.md)
* [Data Table Column Extensions for Angular](../ui/angular/data-table-column-extensions.md)

#### Dynamic Form Extensions

Dynamic form extension system allows you to add a new field in the create and/or edit forms on the user interface. See the related documents to learn how to use this system:

* [Dynamic Form Extensions for Angular](../ui/angular/dynamic-form-extensions.md)

#### Page Toolbar

Page toolbar system allows you to add components to the toolbar of any page. See the related documents to learn how to use this system:

* [Page Toolbar Extensions for ASP.NET Core UI](../ui/aspnetcore/page-toolbar-extensions.md)
* [Page Toolbar Extensions for Angular](../ui/angular/page-toolbar-extensions.md)
