# Application startup template

## Introduction

ABP solution is Visual Studio project which provides a layered structure based on the [Domain Driven Design](../Domain-Driven-Design.md) (DDD) best practices. This document explains how to create a new solution, the structure of the solution and the aims of the projects inside the solution.

## Creating a new ABP Commercial solution

There are 2 ways of creating a new ABP Commercial solution. You can use the [ABP CLI](https://docs.abp.io/en/abp/latest/CLI) or [ABP Suite](../abp-suite/add-solution.md).

### Creating solution via ABP CLI

If you have not yet installed ABP CLI, you need to install it with the following command. To check if you already have it, write `abp` to the command line.

````bash
dotnet tool install -g Volo.Abp.Cli
````

To create your solution, go to the directory where you want to create in and type the following command:

````bash
abp new Acme.BookStore -t app-pro
````

* `Acme.BookStore` is the project name. Your Visual Studio Solution, `.sln` file will be named as `Acme.BookStore.sln`.  The projects inside the root folder and all the namespaces will have the prefix `Acme.BookStore.*`.  You can give different project names like only`BookStore` or `Acme.Retail.BookStore`. You can use single level, two-level or three-level naming.

* `-t` or `--template` is the template name. For commercial apps, use `app-pro`. 

* To discover other CLI new project options type:

  ```bash
  abp help new
  ```

  

Note that, if you want to uninstall ABP CLI tool, you can write:

```bash
dotnet tool uninstall -g Volo.Abp.Cli
```

Or to update / reinstall, you can write :

```bash
dotnet tool update -g Volo.Abp.Cli
```

#### Specify the UI framework

The template provides multiple UI frameworks:

* `mvc`: ASP.NET Core MVC UI with Razor Pages (default)
* `angular`: Angular UI

Use `-u` or `--ui` option to specify the UI framework:

````bash
abp new Acme.BookStore -t app-pro -u angular
````

#### Specify the database provider

The template supports the following database providers:

- `ef`: Entity Framework Core (default)
- `mongodb`: MongoDB

Use `-d` or `--database-provider` option to specify the database provider:

````bash
abp new Acme.BookStore -t app-pro -d mongodb
````

### Creating solution via ABP Suite

ABP Suite allows you to create a new ABP solution. 

If you have not installed ABP Suite, see [how to install ABP Suite](../abp-suite/how-to-install.md).

To start ABP Suite, see [how to start ABP Suite](../abp-suite/how-to-start.md). 

To create a new solution, see [how to create a new solution](../abp-suite/create-solution.md). 



## What's next?

- [Solution structure](solution-structure.md)