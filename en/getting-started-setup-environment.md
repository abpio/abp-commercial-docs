# Getting Started

````json
//[doc-params]
{
    "UI": ["MVC", "Blazor", "BlazorServer", "NG"],
    "DB": ["EF", "Mongo"],
    "Tiered": ["Yes", "No"]
}
````

> This tutorial assumes that you've already purchased an [ABP Commercial license](https://commercial.abp.io/pricing) and have an active ABP Commercial account.

> This document assumes that you prefer to use **{{ UI_Value }}** as the UI framework and **{{ DB_Value }}** as the database provider. For other options, please change the preference on top of this document.

## Setup your development environment

First things first! Let's setup your development environment before creating the first project.

### Pre-requirements

The following tools should be installed on your development machine:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) (v16.8+) for Windows / [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). <sup id="a-editor">[1](#f-editor)</sup>
* [.NET Core 5.0+](https://www.microsoft.com/net/download/dotnet-core/)
{{ if UI != "Blazor" }}
* [Node v12 or v14](https://nodejs.org/)
* [Yarn v1.20+ (not v2)](https://classic.yarnpkg.com/en/docs/install) <sup id="a-yarn">[2](#f-yarn)</sup> or npm v6+ (already installed with Node)
{{ end }}
{{ if Tiered == "Yes" }}
* [Redis](https://redis.io/) (as the [distributed cache](Caching.md)).
{{ else }}
* [Redis](https://redis.io/) (as the [distributed cache](Caching.md)) is required if you select the Public website option.
{{ end }}

<sup id="f-editor"><b>1</b></sup> _You can use another editor instead of Visual Studio as long as it supports .NET Core and ASP.NET Core._ <sup>[↩](#a-editor)</sup>

{{ if UI != "Blazor" }}

<sup id="f-yarn"><b>2</b></sup> _Yarn v2 works differently and is not supported._ <sup>[↩](#a-yarn)</sup>

{{ end }}

### Install the ABP CLI

[ABP CLI](https://docs.abp.io/en/abp/latest/CLI) is a command line interface that is used to automate some common tasks for ABP based solutions.

> ABP CLI is a free & open source tool for [the ABP framework](https://abp.io/). It is also used for ABP Commercial application development.

First, you need to install the ABP CLI using the following command:

````shell
dotnet tool install -g Volo.Abp.Cli
````
If you've already installed, you can update it using the following command:
````shell
dotnet tool update -g Volo.Abp.Cli
````
#### Login to your account

In order to use ABP Commercial features, you need to login your account using the ABP CLI:

````shell
abp login <username>
````

It will ask a password, so you must enter the password of your account.

> You can freely create a new account from the [ABP Account web site](https://account.abp.io/Account/Register). However, your account should be registered to an organization as a developer to be able to use the ABP Commercial. If your company has an ABP Commercial license, ask your manager to add your account to the developer list of the organization.

### Install the ABP Suite

[ABP Suite](abp-suite/index.md) is an application aims to assist you on your development.

First, you need to install the ABP Suite:

````shell
abp suite install
````

If you've already installed, you can update it:

````shell
abp suite update
````

## Next Step

* [Creating a new solution](getting-started-create-solution.md)