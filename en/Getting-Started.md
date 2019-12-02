# Getting Started

> This tutorial assumes that you've already purchased an [ABP Commercial license](https://commercial.abp.io/pricing) and have an active ABP Commercial account.

## Setup Your Development Environment

First things first! Let's setup your development environment before creating the first project.

### Install the ABP CLI

[ABP CLI](https://docs.abp.io/en/abp/latest/CLI) is a command line interface that is used to authenticate and automate some tasks for ABP based applications.

> ABP CLI is a free & open source tool for [the ABP framework](https://abp.io/). It is also used for ABP Commercial application development.

First, you need to install the ABP CLI using the following command:

````shell
dotnet tool install -g Volo.Abp.Cli
````

If you've already installed, you can update it using the following command:

````shell
dotnet tool update -g Volo.Abp.Cli
````

#### Login to Your Account

In order to use ABP Commercial features, you need to login your account using the ABP CLI:

````shell
abp login <username>
````

It will ask a password, so you enter the password of your account.

> You can freely create a new account from the [ABP Account web site](https://account.abp.io/Account/Register). However, your account should be registered to an organization as a developer to be able to use the ABP Commercial. If your company has an ABP Commercial license, ask your manager to add your account to the developer list of the organization.

### Install the ABP Suite

[ABP Suite](Abp-Suite/Index.md) is an application aims to assist you on your development.

First, you need to install the ABP Suite:

````shell
dotnet tool install -g Volo.Abp.Suite
````

If you've already installed, you can update it:

````shell
dotnet tool update -g Volo.Abp.Suite
````

## Create a New Project

There are two ways of creating a new project: ABP Suite and ABP CLI.

### Using ABP Suite to Create a New Project

TODO

### Using ABP CLI to Create a New Project

Use the `new` command of the ABP CLI to create a new project:

````shell
abp new Acme.BookStore -t app-pro
````

* `-t` argument specifies the [startup template](Startup-Templates/Index.md) name. `app-pro` is the startup template that contains the essential [ABP Commercial Modules](https://commercial.abp.io/modules) pre-installed and configured for you.

#### Project Creation Options

ABP CLI has some default values those can be changed by specifying the available options.

##### UI Framework

Use `-u` or `--ui` option to specify the UI framework:

````shell
abp new Acme.BookStore -t app-pro -u angular
````

All possible values for the UI framework:

- `mvc`: ASP.NET Core MVC UI with Razor Pages (default).
- `angular`: Angular UI.

##### Database Provider

Use `-d` (or `--database-provider`) option to specify the database provider:

```bash
abp new Acme.BookStore -d mongodb
```

All possible values for the database provider:

- `ef`: Entity Framework Core (default)
- `mongodb`: MongoDB

#### ABP CLI Commands & Options

> [ABP CLI document](https://docs.abp.io/en/abp/latest/CLI) covers all of the available commands and options for the ABP CLI. The main difference for the ABP Commercial is the template names. See the [ABP Commercial Startup Templates](Startup-Templates/Index.md) document for other commercial templates.

