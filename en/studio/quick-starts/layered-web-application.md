# Quick Start: Creating a Layered Web Application

Welcome to ABP Studio. In this quick start guide, you will learn how to create and run a layered (and potentially modular) web application using ABP Studio.

## Creating a New Solution

> ABP Commercial startup solution templates have many options for your specific needs. If you don't understand an option that probably means you don't need it. We selected common defaults for you, so you can leave these options as they are.

Assuming that you have [installed and logged in](../installation.md) to the application, you should see the following screen when you open ABP Studio:

![abp-studio-welcome-screen](images/abp-studio-welcome-screen.png)

Select the *File* -> *New Solution* in the main menu, or click the *New solution* button on the Welcome screen to open the *Create new solution* wizard:

![abp-studio-new-solution-dialog](images/abp-studio-new-solution-dialog.png)

We will use the *Application (Layered)* solution template for this tutorial, so pick it and click the *Next* button:

![abp-studio-new-solution-dialog-solution-properties](images/abp-studio-new-solution-dialog-solution-properties.png)

In that screen, you choose a name for your solution. You can use different level of namespaces; e.g. `BookStore`, `Acme.BookStore` or `Acme.Retail.BookStore`.

Then select an *output folder* to create your solution. The *Create solution folder* option will create a folder in the given output folder with the same name of your solution.

Once your configuration is done, click the *Next* button to navigate to the *UI Framework* selection:

![abp-studio-new-solution-dialog-ui-framework](images/abp-studio-new-solution-dialog-ui-framework.png)

Here, you see all the possible UI options supported by that startup solution template. You can pick your favorite one.

Notice that; Once you select a UI type, some additional options will be available under the UI Framework list. You can further configure the options or leave them as default and click the *Next* button for the *UI Theme* selection screen:

![abp-studio-new-solution-dialog-ui-theme](images/abp-studio-new-solution-dialog-ui-theme.png)

LeptonX is the suggested UI theme that is proper for production usage. Select one of the theme and configure additional options and click the *Next* button for the *Mobile Framework* selection:

![abp-studio-new-solution-dialog-mobile-framework](images/abp-studio-new-solution-dialog-mobile-framework.png)

Here, you see all the mobile applications available in that startup solution template. These mobile applications are well-integrated to your solution and can use the same backend with your web application. They are simple (not have pre-built features as much as the web application) but a very good starting point to build your mobile application.

Pick the one best for you, or select the *None* if you don't want a mobile application in your solution, then click Next to navigate to the *Additional UI options* screen:

![abp-studio-new-solution-dialog-additional-ui-options](images/abp-studio-new-solution-dialog-additional-ui-options.png)

That startup solution template also provides an option to create a second web application inside the solution. The second application is called the Public website, an ASP.NET Core MVC / Razor Page application. It can be used to create a public landing/promotion for your product. It is well integrated to the solution (can share same services, entities, database and the same authentication logic, for example). If you want, you can also include the [CMS Kit module](../../modules/cms-kit/index.md) to your solution add dynamic content features to your web application.

So, either select the *Public website* or skip it and click the Next button for the *Solution Structure* selection:

![abp-studio-new-solution-dialog-solution-structure](images/abp-studio-new-solution-dialog-solution-structure.png)

The *Tiered* option is used to physically separate the web application (the UI part) from the backend HTTP APIs. It creates a separate host application that only serves the HTTP (REST) APIs. The web application then performs remote HTTP calls to that application for every operation. If the *Tiered* option is not selected, then the web and HTTP APIs are hosted in a single application, and the calls from the UI layer to the API layer are performed in-process.

The tiered architecture allows you to host the web (UI) application in a server that can not access to your database server. However, it brings a slight loss of performance (because of the HTTP calls between UI and HTTP API applications) and makes your architecture, development and deployment more complex. If you don't understand the tiered structure, just skip it.

After making your *Tiered* selection, you can click the *Next* button for the *Database Provider* selection:

![abp-studio-new-solution-dialog-database-provider](images/abp-studio-new-solution-dialog-database-provider.png)

On that screen, you can decide on your database provider by selecting one of the provided options. There are some additional options for each database provider. Leave them as default or change them based on your preferences, then click the *Next* button for additional *Database Configurations*:

![abp-studio-new-solution-dialog-database-configurations](images/abp-studio-new-solution-dialog-database-configurations.png)

Here, you can select the DBMS (only for Entity Framework Core) and the connection string. Now, we are ready to allow ABP Studio to create our solution. Just click the *Create* button and let it go.

After clicking the Create button, the dialog is closed and your solution is loaded into ABP Studio:

![abp-studio-created-new-solution](images/abp-studio-created-new-solution.png)

You can explore the solution, but you need to wait for background tasks to be completed before running any application in the solution.

d