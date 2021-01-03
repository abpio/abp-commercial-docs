# Why ABP.IO Platform?

This document aims to answer to the big question: "Why should you use the ABP.IO Platform instead of creating a new solution from scratch?"

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

ABP **pre-integrates** the **popular**, **mature** and **up to date libraries** into the solution. You don't spend your time to integrate them and talk to each other. They properly work out of the box.

### UI Theme

When it comes to the UI, there are a lot of challenges including to prepare a foundation to create a **responsive**, **modern** and **flexible** UI kit with a consistent look & feel and tons of features (like left/top navigation menu, header, toolbar, footer, widgets... etc).

Even if you buy a pre-built theme, **integrating** it to your solution may take **days of development**. Upgrading such a theme is another problem. Most of the times, the theme's HTML/CSS structure is mixed to your UI code and it is not easy to **upgrade** or **change** the theme later.

ABP Framework provides a [theming](https://docs.abp.io/en/abp/latest/UI/AspNetCore/Theming) system that makes your UI code independent from the theme. Themes are isolated and they are **NuGet/NPM packages**. Installing or upgrading a theme is just a minute. While you can build your own theme (or integrate an existing theme), ABP Commercial offers **professional and modern** [themes](https://commercial.abp.io/themes).

### Coding Standards & Training

Once you create the solution that is ready for development, you typically need to **train the developers** to make them understand the system and develop it with the same **conventions** in a **standard** and consistent way. Even if you train the developers, it is **hard to prepare and maintain a documentation** to explain how to make development. So, by the time, every developer will write the code in a different style and coding standards will begin to diverge.

ABP solution is already **well-defined** and **well-documented**. [Tutorials](https://docs.abp.io/en/abp/latest/Tutorials/Part-1) and [best practice guides](https://docs.abp.io/en/abp/latest/Best-Practices/Index) clearly explain how to make development on this solution.

## Don't Repeat Yourself!

Creating a base solution takes a significant amount of time and requires a good architectural experience. However, this is just the beginning! As you start developing, you will find that you have to write lots of repetitive code that would be great if all this could be handled automatically.

### Cross-Cutting Concerns

Cross-Cutting Concerns are the fundamental repeating logic that should be implemented for each use case. Some examples;

*   Starting **transactions**, committing on success and rollback on errors.
*   **Handling and reporting exceptions**, returning a proper error response to the clients. Handling error cases in the client-side.
*   Implementing **authorization** and **validation**, returning proper responses and handling these in the client-side.
*   Writing **audit logs** and **entity histories** to track when an entity is changed by a user.

ABP Framework automates or simplifies all the standard cross-cutting concerns. You only write code that matters for your business and ABP handles the rest by conventions.

### Architectural Infrastructure

You typically need to build **infrastructure** to properly implement your **architecture**. For example, you generally implement some kind of the **Repository** pattern. You define some **base classes** to **simplify** and **standardize** to create entities, services, controllers and other kinds of objects.

All these infrastructure, even in the most simple way, takes a lot of time to **build**, **maintain** and **document**. It gets bigger by the time and becomes hard to maintain it in your solution. Separating these into a reusable project is the starting point to build your own **internal framework**.

ABP Framework provides all these and more out of the box. It is mature and well documented.

### Enterprise Application Patterns

TODO
