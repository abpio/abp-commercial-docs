# Creating a New Solution

You can use the [ABP CLI](https://docs.abp.io/en/abp/latest/CLI) to create a new project using this startup template. Or alternatively, you can directly create this startup template via [Suite](../../abp-suite/index.md). 

## Creating a New Solution via ABP CLI

Firstly, install the [ABP CLI](https://docs.abp.io/en/abp/latest/CLI) if you haven't installed it before:

```bash
dotnet tool install -g Volo.Abp.Cli
```

Then, use the `abp new` command in an empty folder to create a new solution:

```bash
abp new Acme.BookStore -t app-nolayers-pro
```

* `Acme.BookStore` is the solution name, like *YourCompany.YourProduct*. You can use single-level, two-level or three-level naming.
* In this example, the `-t` (or alternatively `--template`) option specifies the template name.

### Specify the UI Framework

This template provides multiple UI frameworks:

* `mvc`: ASP.NET Core MVC UI with Razor Pages (default)
* `blazor-server`: Blazor Server UI
* `angular`: Angular UI
* `none`: Without UI (for HTTP API development)

> This template doesn't have Blazor WebAssembly UI, because it requires at least 3 projects (server-side, UI and shared library between these two projects). We are recommending using the layered [application startup template](../application/index.md) for Blazor WebAssembly projects.

Use the `-u` (or `--ui`) option to specify the UI framework while creating the solution:

```bash
abp new Acme.BookStore -t app-nolayers-pro -u angular
```

This example specifies the UI type (the `-u` option) as `angular`. You can also specify `mvc`, `blazor-server` or `none` for the UI type.

### Specify the Database Provider

This template supports the following database providers:

* `ef`: Entity Framework Core (default)
* `mongodb`: MongoDB

Use the `-d` (or `--database-provider`) option to specify the database provider while creating the solution:

```bash
abp new Acme.BookStore -t app-nolayers-pro -d mongodb
```

## Creating a New Solution via Suite

[ABP Suite](../../abp-suite/index.md) allows you to create a new ABP solution from UI.

* If you have not installed ABP Suite, see [how to install ABP Suite](../../abp-suite/how-to-install.md).
* To start ABP Suite, see [how to start ABP Suite](../../abp-suite/how-to-start.md).
* To create a new solution, see [how to create a new solution](../../abp-suite/create-solution.md).

## What's Next?

* [Solution Structure](./solution-structure.md)
