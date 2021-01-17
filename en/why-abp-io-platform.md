# Why ABP.IO Platform?

This document aims to answer to the big question:

> "Why should you use the ABP.IO Platform instead of creating a new solution from scratch?"

The document introduces the challenges of building a modern software solution and explains how ABP addresses these challenges.

## Creating a New Solution

When you need to start a new solution, there are **a lot of questions** you need to ask yourself and you should spend **a lot of time** before starting to write your **very first business code**.

### Creating an Empty Solution

Even creating an almost-empty solution is challenging;

*   How do you **organize your codebase** across projects?
*   What are the **layers** and how they interact with each other?
*   How do you **integrate** to 3rd-party library and systems?
*   How to setup the automated **tests**?

ABP provides a **well-architected**, **layered** and **production-ready** [startup solution](https://docs.abp.io/en/abp/latest/Startup-Templates/Application) based on the [Domain Driven Design](https://docs.abp.io/en/abp/latest/Domain-Driven-Design) principles. The solution also includes a pre-configured **unit** and **integration** [test](https://docs.abp.io/en/abp/latest/Testing) projects for each layer.

Creating such a solution structure requires a good **architectural experience** and a **significant time to prepare** it. ABP provides it in just one minute.

### Common Libraries

Which **libraries** you should use to implement for **common requirements**? Software development ecosystem is highly dynamic and it is **hard to follow** the latest tools, libraries, trends and approaches.

ABP **pre-integrates** the **popular**, **mature** and **up to date libraries** into the solution. You don't spend your time to integrate them and talking to each other. They properly work out of the box.

### UI Theme & Layout

When it comes to the UI, there are a lot of challenges including to prepare a foundation to create a **responsive**, **modern** and **flexible** UI kit with a consistent look & feel and tons of features (like left/top navigation menu, header, toolbar, footer, widgets... etc).

Even if you buy a pre-built theme, **integrating** it to your solution may take **days of development**. Upgrading such a theme is another problem. Most of the times, the theme's HTML/CSS structure is mixed to your UI code and it is not easy to **upgrade** or **change** the theme later.

ABP Framework provides a [theming](https://docs.abp.io/en/abp/latest/UI/AspNetCore/Theming) system that makes your UI code independent from the theme. Themes are isolated and they are **NuGet/NPM packages**. Installing or upgrading a theme is just a minute. While you can build your own theme (or integrate an existing theme), ABP Commercial offers **professional and modern** [themes](https://commercial.abp.io/themes).

> There are also UI **component providers** (like Telerik and DevExpress). But they only provide individual components. You are the responsible to create your own **layout system**. You **can use** such libraries in your ABP based solutions just like using in any other project.

### Test Infrastructure

Preparing a robust test environment takes a time. You need to **setup test projects** in your solution, select the tools, **mock the services and database**, create some base classes and utility services to reduce repeating code in the tests and so on.

ABP Startup Templates comes with the test projects already configured for you and you can immediately write your first unit or integration test code. It is very clear [how to write tests](https://docs.abp.io/en/abp/latest/Testing) with the ABP Framework.

### Coding Standards & Training

Once you create the solution that is ready for development, you typically need to **train the developers** to make them understand the system and develop it with the same **conventions** in a **standard** and consistent way. Even if you train the developers, it is **hard to prepare and maintain a documentation** to explain how to make the development. So, by the time, every developer will write the code in a different style and coding standards will begin to diverge.

ABP solution is already **well-defined** and **well-documented**. [Tutorials](https://docs.abp.io/en/abp/latest/Tutorials/Part-1) and [best practice guides](https://docs.abp.io/en/abp/latest/Best-Practices/Index) clearly explain how to make development on this solution.

### Keeping Your Solution Up to Date

After starting your development, you need to follow the libraries you are using for upgrades/patches. We regularly update all packages to the latest versions and testing them with the framework. So, when you update the ABP Framework, all its dependencies are also upgraded.

`abp update` [CLI](https://docs.abp.io/en/abp/latest/CLI) command automatically discovers and upgrades all ABP related NuGet and NPM packages in a solution in a few seconds. With ABP, it is easier to stay with the latest versions.

## Don't Repeat Yourself!

Creating a base solution takes a significant amount of time and requires a good architectural experience. However, this is just the beginning! As you start developing, you will find that you have to write lots of repetitive code that would be great if all this could be handled automatically.

ABP automates or simplifies repeating code as much as possible by following the convention over configuration principle. However, it doesn't restrict you when you need to do things manually.

### Authentication

Single Sign On, Active Directory / LDAP Integration, IdentityServer integration, social logins, two factor authentication, forgot/reset password, email activation, new user registration, password complexity control, locking account on failed attempts, logging and reporting the login attempts... etc. Most of these requirements are familiar to you? You are not alone!

ABP Framework and ABP commercial provides all these standard stuff pre-implemented for you as a reusable account module. You just enable and configure what you need.

### Cross-Cutting Concerns

Cross-Cutting Concerns are the fundamental repeating logic that should be implemented for each use case. Some examples;

*   Starting **transactions**, committing on success and rollback on errors.
*   **Handling and reporting exceptions**, returning a proper error response to the clients. Handling error cases in the client-side.
*   Implementing **authorization** and **validation**, returning proper responses and handling these in the client-side.

ABP Framework automates or simplifies all the standard cross-cutting concerns. You only write code that matters for your business and ABP handles the rest by conventions.

### Architectural Infrastructure

You typically need to build **infrastructure** to properly implement your **architecture**. For example, you generally implement some kind of the **Repository** pattern. You define some **base classes** to **simplify** and **standardize** to create entities, services, controllers and other kinds of objects.

ABP Framework provides all these and more out of the box. It is mature and well documented.

### Enterprise Application Requirements

There are a lot of requirements you repeatedly implement in every business application;

* Detailed **permission system** and managing permissions on the UI based on roles and users.
* Writing **audit logs** and **entity histories** to track when an entity is changed by a user.
* Making your entities **soft delete**, so they are marked as deleted instead of really deleting from the database and automatically filtering deleted entities on your queries.
* Creating abstractions and wrappers to **consume your backend APIs** from the frontend code.
* Enqueuing and executing **background jobs**.
* Handling multiple **time zones** in a global system.
* Sharing **validation**, **localization**, **authorization** logic between server and client.

There are a lot of more. ABP provides infrastructure to implement such requirements easily. Again, you don't spend your valuable time to re-implement all these again and again.

### Generating Initial Code & Tooling

[ABP Suite](https://commercial.abp.io/tools/suite) can generate a full stack CRUD page for your entities in a few seconds. The generated code is layered and clean. All the standard validation and authorization requirements are implemented. Once you get a fully running page in seconds, you can change and improve the code based on your business requirements.

### Integrating to 3rd-Party Libraries and Systems

Most of the libraries are designed as **low level** and you typically do some amount of work to properly integrate them **without repeating** the same integration and configuration code everywhere in your solution.

For example assume that you need to use **RabbitMQ** as a distributed event bus. All you want to send a message to a queue and handle the messages with some code in your solution. But you need to understand **messaging patterns**, queue and **exchange details**. If you want to write an efficient code, you need to create a **pool** to manage connections, client and channels. You need to deal with **exceptions**, ACK messages, **re-connecting** to RabbitMQ on failures and more.

ABP's RabbitMQ Distributed Event Bus integration abstracts all these details. You just send and receive messages. You need to write low level code? No problem, you can always do. ABP doesn't restrict you when you need to use low level features of the library you are using.

### Fine Tune ASP.NET Core & the Libraries

There are so many popular frameworks, tools and libraries it's hard to know where to start! This is one of the most common stresses among the developers. There are just so many tools and libraries out there. It is difficult to know if you're going down the right path. ABP picks up the best tools and libraries for you. Orchestrates and sets them up nicely. Decreases boilerplate startup code of the libraries.

### Tons of Features

Tag helpers, dynamic forms, BLOB storing system and other ABP features helps you to keep DRY and focus on your own business.

### Why Not Build Your Own Framework?

All these infrastructure, even in the most simple way, takes a lot of time to **build**, **maintain** and **document**. It gets bigger by the time and becomes hard to maintain it in your solution. Separating these into a reusable project is the starting point to build your own **internal framework**.

Building, documenting, training and maintaining an internal framework is really hard. If you don't have a large dedicated framework team, your internal framework rapidly turns into an undocumented legacy code that no one can understand and maintain anymore.

## Architectural Infrastructure

### Modularity

Building a true modular system is not easy. All the aspects of the system (database, entities, APIs, UI pages/components) can be splitted into modules and each module can be reusable without others. Plain ASP.NET Core doesn't provide such a modular architecture. If you need it, you should think on it from scratch.

ABP Framework is designed to build modular applications and systems. Every feature in the framework is developed so it is compatible with the modularity. Documentation and guides explain how to develop reusable modules in a standard way.

### SaaS / Multi-Tenancy

[Multi-Tenancy](https://docs.abp.io/en/abp/latest/Multi-Tenancy) is a common way to implement SaaS systems. However, implementing a consistent multi-tenant infrastructure may become complicated.

ABP Framework provides a complete multi-tenant infrastructure and abstract complexity from your business code. Your application code will be mostly multi-tenancy aware while the ABP Framework automatically isolates database, cache and other details of the tenants from each other. It supports single database, per tenant database and hybrid approaches. It properly configures the libraries like Microsoft Identity and IdentityServer those are not multi-tenancy compatible normally.

### Microservices

If you are building a microservice system, you need to deal a lot of infrastructure details: Authenticating and authorizing applications and microservices, implementing asynchronous (messaging) and synchronous (REST/GRPC) communication patterns between microservices are the most fundamental issues.

ABP Framework provides services, [guides](https://docs.abp.io/en/abp/latest/Microservice-Architecture) and samples to help you on implementing your own microservice solution using the industry standard tools.

ABP Commercial even goes one step further and provides a complete [startup template](startup-templates/microservice/index.md) to start your microservice solution.

## Pre-Built Modules

To be honest, all we have similar, but slightly different business requirements. However, we all should re-invent the wheel since no one's code can directly work in our solution. They are all embedded part of a larger solution.

ABP Commercial [modules](https://commercial.abp.io/modules) provides a lot of **re-usable application modules** like payment, chat, file management, audit log reporting... etc. All of these modules are easily installed into your solution and directly work. We are constantly adding more modules.

All modules are designed as customizable for your business requirements. If you need to have a complete control, you can download the full source code of any module and completely customize based on your specific business requirements.

## ABP Community

Being in a big community that everyone follows similar coding styles, principles and share a common infrastructure brings power when you have troubles or you need to get help on design decisions. Since we write code in a similar way, we can to help each other much better. ABP Community is one of the most active communities on GitHub.

It is easy to share code, or even reusable libraries between ABP developers. A code works for you will also work for others. There are a lot of samples and tutorials that you can directly implement for your application.

When you hire a developer worked before with the ABP architecture will immediately understand your solution and start development in a very short time.

## Questions You May Ask

### Is the learning curve high?

We can easily say the learning curve is much lower than not using the ABP Framework. That may sound surprising, but let us to explain it;

ABP creates a full stack, production ready, working solution for you in a few seconds. Many of the real-life problems are already solved and many fine tune configurations are already applied for the ASP.NET Core and the other used libraries. If you start from scratch, you will experience and learn all these details yourself to truly implement for your solution.

ABP uses the industry standard frameworks, libraries and systems you already know (or need to learn to build a real world product) like EF Core, AutoMapper, IdentityServer, Bootstrap, Redis, Angular, Blazor, SignalR... etc. So, all the knowledge you have is directly re-usable with the ABP Framework. ABP even simplifies to use these libraries and systems and solves the integration problems. If you don't know these tools now, learning them will be easier within the ABP Framework.

ABP provides an awesome infrastructure to apply DDD principles and other best practices. It provides a lot of sweet abstractions and automations to reduce the repeating code. However, it doesn't force you to use or apply all these. A common mistake is to see that ABP has a lot of features and hard to learn all. Having a lot of features is an advantage when you come to the point that you need them. However, until you need a feature, you don't need to know it and you can continue the development approach you like. You can write your code just like ABP doesn't provide all these benefits for you if you want to do it or when you don't know how to do with the ABP Framework. Learning ABP infrastructure is progressive. You will love it whenever you learn a new feature, but can continue to the development without knowing their existence.

Finally, learning an ABP Framework feature is much more easier building the same feature yourself when you need it (and believe us you will need it soon or later). We've done the hard work for you and documented well. We believe that you will enjoy and love it as you learn.

### Is it an overhead for simple CRUD applications?

ABP Framework pretty simplifies building CRUD applications, because CRUD applications are more suitable to automate.

ABP provides default repositories, CRUD application service base class, pre-built DTO classes, simplified client-to-server communication approach, UI components, helpers and more to build CRUD functionalities for your applications in the production quality.

ABP Suite even automatically creates production level CRUD functionality when you just define the properties of your entity.

Finally, most of the applications and systems are thought as simple at the beginning, but they grow by the time. Once your application grows and you need to implement some advanced requirements, ABP will be here to help you even more.

### Is it sufficient for complex systems?

ABP Framework is designed to solve real-time problems and build complex systems. It provides a lot of infrastructure requirements already implemented for your system and provides lots of integrations to enterprise systems and tools.

We definitely know that there is no limit of the **mountain of complexity** and no one can provide everything to you. **ABP Platform is a helicopter that puts you at a high point of the mountain and provides many useful tools to help you climb the rest yourself.**

### What if I want to customize it?

Customization has different types and levels;

* ABP Framework itself is highly customizable. You can almost replace and override any service. 3rd-party dependencies are abstracted and generally multiple alternatives are provided. You can implement your own integrations to extend the framework.
* Modules are designed to be customizable and extensible from database to the user interface. The Framework provides a [standard extensibility model](https://docs.abp.io/en/abp/latest/Customizing-Application-Modules-Guide) that is implemented wherever possible.
* Modules are designed as layered and compatible with [different architectures](https://docs.abp.io/en/abp/latest/Best-Practices/Index). They can be used as a part of a monolithic application or they can be deployed as standalone microservices with their own databases.
* Modules are independent from each other. You can remove one module without touching to others.

For most of the applications, these are more than necessary. However, it can not be the same freedom as the code in your own solution. For such cases, you can always replace a module by its source code when you want to completely change it. For ABP Commercial modules, we always provide a source code option.

### What if I need to bypass ABP abstractions?

ABP provides a lot of abstractions on top of the ASP.NET Core and some other libraries to simplify development and increase your productivity. However, it is always possible to not use ABP abstractions and directly use the underlying API.

For example, ABP provides repositories to simplify data access. However, if you prefer, you can directly access to the DbContext and use any Entity Framework Core API as you can do it in any .NET project.

### Can I use other database systems?

ABP provides Entity Framework Core (you can use almost any relational database system) and MongoDB integrations out of the box. In addition, it provides EF Core compatible Dapper integration. All pre-built modules have EF Core and MongoDB options.

If you need to use any other database system, you are free to use its own libraries and APIs. ABP Framework doesn't have any restrictions here. For example, you can use Cassandra for your own application code. If you use a pre-built module, it will probably not have a Cassandra integration package. You have two options here: You can implement the Cassandra integration yourself (by implementing a few  repository interfaces defined by the module) or let the module continue to use its own database. Multiple database systems can run in a single application without any problem.

### Does ABP slows my application?

ABP automates a lot of common tasks that you would implement them manually if you didn't use it. Some examples are exception handling, validation, authorization, unit of work (and transaction management), audit logging... etc.

If you'd written all these code yourself and if they run in every HTTP request, wouldn't it make your application code a little slower? So, yes, ABP will make your application slightly slower. The good news is that the overhead is not much and the ABP features are already optimized. If you start to disable these ABP features, you will see the overhead decreases. However, you typically need these features and the slight performance difference is acceptable. As said before, if you would write these yourself, it would have a similar performance overhead.

So, for most of the systems, the performance overhead can be safely ignored. For others, you can disable the ABP features you don't need to.

***TODO: We will share benchmarks***

### Is ABP a CMS?

**ABP is not a CMS** (Content Management System). It is a **generic business application development framework**. The Framework doesn't make any assumption for CMS or any type of applications. It is well layered so that the core modules are Web or ASP.NET Core independent and can be used to develop console applications, background services or any type of .NET compatible application.

By the way ABP Framework provides modularity and a CMS module or CMS system can be developed on top of it. We are currently building such a CMS Kit module for the ABP Framework that provides CMS primitives and features to develop your own CMS application.