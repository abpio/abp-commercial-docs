# Getting Started

````json
//[doc-params]
{
    "UI": ["MVC", "Blazor", "BlazorServer", "NG", "MAUIBlazor"],
    "DB": ["EF", "Mongo"],
    "Tiered": ["Yes", "No"]
}
````

> This tutorial assumes that you've already purchased an [ABP Commercial license](https://commercial.abp.io/pricing) and have an active ABP Commercial account.

> This document assumes that you prefer to use **{{ UI_Value }}** as the UI framework and **{{ DB_Value }}** as the database provider. For other options, please change the preference on top of this document.

## Create the Database

### Connection String

Check the **connection string** in the `appsettings.json` file under the {{if Tiered == "Yes"}}`.AuthServer` and `.HttpApi.Host` projects{{else}}{{if UI=="MVC"}}`.Web` project{{else if UI=="BlazorServer"}}`.Blazor` project{{else}}`.HttpApi.Host` project{{end}}{{end}}.

{{ if DB == "EF" }}

````json
"ConnectionStrings": {
  "Default": "Server=localhost;Database=BookStore;Trusted_Connection=True"
}
````

> **About the Connection Strings and Database Management Systems**
>
> The solution is configured to use **Entity Framework Core** with **MS SQL Server** by default. However, if you've selected another DBMS using the `-dbms` parameter on the ABP CLI `new` command (like `-dbms MySQL`), the connection string might be different for you.
>
> EF Core supports [various](https://docs.microsoft.com/en-us/ef/core/providers/) database providers and you can use any supported DBMS. See [the Entity Framework integration document](https://docs.abp.io/en/abp/latest/Entity-Framework-Core) to learn how to [switch to another DBMS](https://docs.abp.io/en/abp/latest/Entity-Framework-Core-Other-DBMS) if you need later.

### Database Migrations

The solution uses the [Entity Framework Core Code First Migrations](https://docs.microsoft.com/en-us/ef/core/managing-schemas/migrations/?tabs=dotnet-core-cli). It comes with a `.DbMigrator` console application which **applies the migrations** and also **seeds the initial data**. It is useful on **development** as well as on **production** environment.

> `.DbMigrator` project has its own `appsettings.json`. So, if you have changed the connection string above, you should also change this one. 

### The Initial Migration

`.DbMigrator` application automatically **creates the Initial migration** on first run. 

**If you are using Visual Studio, you can skip to the *Running the DbMigrator* section.** However, other IDEs (e.g. Rider) may have problems for the first run since it adds the initial migration and compiles the project. In this case, open a command line terminal in the folder of the `.DbMigrator` project and run the following command:

````bash
dotnet run
````

For the next time, you can just run it in your IDE as you normally do.

### Running the DbMigrator

Right click to the `.DbMigrator` project and select **Set as StartUp Project**

![set-as-startup-project](images/set-as-startup-project.png)

 Hit F5 (or Ctrl+F5) to run the application. It will have an output like shown below:

 ![db-migrator-output](images/db-migrator-output.png)

> Initial [seed data](https://docs.abp.io/en/abp/latest/Data-Seeding) creates the `admin` user in the database (with the password is `1q2w3E*`) which is then used to login to the application. So, you need to use `.DbMigrator` at least once for a new database.

{{ else if DB == "Mongo" }}

````json
"ConnectionStrings": {
  "Default": "mongodb://localhost:27017/BookStore"
}
````

The solution is configured to use **MongoDB** in your local computer, so you need to have a MongoDB server instance up and running or change the connection string to another MongoDB server.

### Seed initial data

The solution comes with a `.DbMigrator` console application which **seeds the initial data**. It is useful on **development** as well as on **production** environment.

> `.DbMigrator` project has its own `appsettings.json`. So, if you have changed the connection string above, you should also change this one. 

Right click to the `.DbMigrator` project and select **Set as StartUp Project**

![set-as-startup-project](images/set-as-startup-project.png)

 Hit F5 (or Ctrl+F5) to run the application. It will have an output like shown below:

 ![db-migrator-output](images/db-migrator-output.png)

> Initial [seed data](https://docs.abp.io/en/abp/latest/Data-Seeding) creates the `admin` user in the database (with the password is `1q2w3E*`) which is then used to login to the application. So, you need to use `.DbMigrator` at least once for a new database.

{{ end }}

## Run the application

{{ if UI == "MVC" || UI == "BlazorServer" }}

> Warning: When you create an ABP solution, the client-side packages are being restored by ABP CLI and Suite. But if you fetch the source-code that's commited by another team member, your `libs` folder will be empty. Before starting the application, run `abp install-libs` command in your Web directory to restore the client-side libraries. This will populate the `libs` folder.

{{ if Tiered == "Yes" }}

> Tiered solutions use Redis as the distributed cache. Ensure that it is installed and running in your local computer. If you are using a remote Redis Server, set the configuration in the `appsettings.json` files of the projects below.

1. Ensure that the `.IdentityServer` project is the startup project. Run this application that will open a **login** page in your browser.

> Use Ctrl+F5 in Visual Studio (instead of F5) to run the application without debugging. If you don't have a debug purpose, this will be faster.

You can login, but you cannot enter to the main application here. This is **just the authentication server**.

2. Ensure that the `.HttpApi.Host` project is the startup project and run the application which will open a **Swagger UI** in your browser.

![swagger-ui](images/swagger-ui.png)

This is the HTTP API that is used by the web application.

3. Lastly, ensure that the {{if UI=="MVC"}}`.Web`{{else}}`.Blazor`{{end}} project is the startup project and run the application which will open a **welcome** page in your browser

![mvc-tiered-app-home](images/mvc-tiered-app-home-2.png)

Click to the **login** button which will redirect you to the *authentication server* to login to the application:

![bookstore-login](images/bookstore-login-3.png)

{{ else # Tiered != "Yes" }}

Ensure that the {{if UI=="MVC"}}`.Web`{{else}}`.Blazor`{{end}} project is the startup project. Run the application which will open the **login** page in your browser:

> Use Ctrl+F5 in Visual Studio (instead of F5) to run the application without debugging. If you don't have a debug purpose, this will be faster.

![bookstore-login](images/bookstore-login-3.png)

{{ end # Tiered }}

{{ else # UI != "MVC" || BlazorServer }}

#### Running the HTTP API Host (Server Side)

{{ if Tiered == "Yes" }}

> Tiered solutions use Redis as the distributed cache. Ensure that it is installed and running in your local computer. If you are using a remote Redis Server, set the configuration in the `appsettings.json` files of the projects below.

Ensure that the `.IdentityServer` project is the startup project. Run the application which will open a **login** page in your browser.

> Use Ctrl+F5 in Visual Studio (instead of F5) to run the application without debugging. If you don't have a debug purpose, this will be faster.

You can login, but you cannot enter to the main application here. This is just the authentication server.

Ensure that the `.HttpApi.Host` project is the startup project and run the application which will open a Swagger UI:

{{ else # Tiered == "No" }}

Ensure that the `.HttpApi.Host` project is the startup project and run the application which will open a Swagger UI:

> Use Ctrl+F5 in Visual Studio (instead of F5) to run the application without debugging. If you don't have a debug purpose, this will be faster.

{{ end # Tiered }}

![swagger-ui](images/swagger-ui.png)

You can see the application APIs and test them here. Get [more info](https://swagger.io/tools/swagger-ui/) about the Swagger UI.

> ##### Authorization for the Swagger UI
>
> Most of the HTTP APIs require authentication & authorization. If you want to test authorized APIs, manually go to the `/Account/Login` page, enter `admin` as the username and `1q2w3E*` as the password to login to the application. Then you will be able to execute authorized APIs too.

{{ end # UI }}

{{ if UI == "Blazor" }}

### Running the Blazor Application (Client Side)

Ensure that the `.Blazor` project is the startup project and run the application.

> Use Ctrl+F5 in Visual Studio (instead of F5) to run the application without debugging. If you don't have a debug purpose, this will be faster.

Once the application starts, click to the **Login** link on to header, which redirects you to the authentication server to enter a username and password:

![bookstore-login](images/bookstore-login-.png)

{{ else if UI == "NG" }}

### Running the Angular Application (Client Side)

Go to the `angular` folder, open a command line terminal, type the `yarn` command (we suggest to the [yarn](https://yarnpkg.com/) package manager while `npm install` will also work)

```bash
yarn
```

Once all node modules are loaded, execute `yarn start` (or `npm start`) command:

```bash
yarn start
```

It may take a longer time for the first build. Once it finishes, it opens the Angular UI in your default browser with the [localhost:4200](http://localhost:4200/) address.


![bookstore-login](images/bookstore-login-3.png)

{{ else if UI == "MAUIBlazor" }}

### Running the MAUI Blazor Application (Client Side)

Ensure that the `.MauiBlazor` project is the startup project.

> Use Ctrl+F5 in Visual Studio (instead of F5) to run the application without debugging. If you don't have a debug purpose, this will be faster.

MAUI supports multiple platforms:

#### Windows

Ensure that the target is `Windows Machine` and run the application.

#### Android

Ensure that the target is `Android Emulators`.

Android emulator is isolated from your development machine network interfaces, you need to configure the network:

**ADB**

Open a command line terminal and run the `adb reverse` command to expose a port on your Android device to a port on your computer. For example:

`adb reverse tcp:44305 tcp:44305`

> You should replace "44305" with the real port.
> You should run the command after starting the emulator.
> You will find that the user avatar cannot be loaded, this is due to the use of insecure https, you can use `Ngrok`.

**Ngrok**

You can use the [ngrok](https://ngrok.com/) to reverse proxy your localhost servies. For example:

`ngrok http 44305`

* You should replace "44305" with the real port.
* You should replace `Authority` with the Ngrok URL.

#### IOS

Ensure that the target is `IOS Emulators`.

The iOS simulator uses the host machine network. Therefore, applications running in the simulator can connect to web services running on your local machine via the machines IP address or via the localhost hostname. For example, given a local secure web service that exposes a GET operation via the /api/todoitems/ relative URI, an application running on the iOS simulator can consume the operation by sending a GET request to https://localhost:<port>/api/todoitems/.

> If the simulator is used from Windows with a remote connection, follow the [Microsoft's documentation](https://docs.microsoft.com/en-us/xamarin/cross-platform/deploy-test/connect-to-local-web-services#specify-the-local-machine-address) to setup a proper configuration.

##### Remote iOS Simulator for Windows

If you are run the MAUI on Mac agent, the remote iOS Simulator can't access backend application running on Windows, you need to run the backend application on Mac or make the backend application on the internal.

### Secure Storage

The MAUI Blazor application uses [Preferences](https://learn.microsoft.com/en-us/dotnet/maui/platform-integration/storage/preferences) to store access token by default, safe practice is to use [Secure Storage](https://learn.microsoft.com/en-us/dotnet/maui/platform-integration/storage/secure-storage), it requires some extra steps for different platforms.

You can check the [Secure Storage documentation](https://learn.microsoft.com/en-us/dotnet/maui/platform-integration/storage/secure-storage#get-started)

#### Got could not find any available provisioning profiles for on ios error!

You need some extra steps, please check the [Microsoft's documentation](https://learn.microsoft.com/en-us/xamarin/ios/get-started/installation/device-provisioning/)

After you run the project, you can click the login button to the login UI.

![bookstore-login](images/bookstore-login-3.png)

{{ end }}

Enter **admin** as the username and **1q2w3E*** as the password to login to the application. 

{{ if UI == "Blazor" }}
![bookstore-home](images/bookstore-home-3.png)
{{else}}
![bookstore-home](images/bookstore-home-3.png)
{{end}}

The application is up and running. You can start developing your application based on this startup template.

## Next

[Web Application development tutorial](tutorials/book-store/part-1.md)
