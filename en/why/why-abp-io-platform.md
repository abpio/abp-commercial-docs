# Why ABP.IO Platform?

This document aims to answer to the big question: "Why should you use the ABP.IO Platform instead of creating your solution from scratch?"

The document introduces the challenges of building a modern software solution and explains how ABP addresses these challenges.

## Creating a New Solution

When you need to start a new solution, there are **a lot of questions** you need to ask yourself and you should spend **a lot of time** before starting to write your **very first business code**.

### Creating an Empty Solution

Even creating an almost-empty solution is challenging;

* How do you **organize your codebase** across projects?
* What are the **layers** and how they interact with each other?
* How do you **integrate** to 3rd-party library and systems?
* How to setup the automated **tests**?

ABP Framework provides a **well-architected** solution structure for you:

![solution-structure](images/solution-structure.png)

*Figure: Example solution structure*

> ABP provides a lot of options while creating a new solution, including multiple UI Framework options (**MVC / Razor Pages**, **Angular** and **Blazor**) and Database Provider options (**EF Core** and **MongoDB**).

The solution structure is well-defined and [documented](https://docs.abp.io/en/abp/latest/Startup-Templates/Application);

* It is **layered** based on the [Domain Driven Design](https://docs.abp.io/en/abp/latest/Domain-Driven-Design) principles.
* It includes pre-configured **unit** and **integration** [test](https://docs.abp.io/en/abp/latest/Testing) projects.

ABP documentation clearly explains how to organize your code in this solution structure;

* The [Tutorials](https://docs.abp.io/en/abp/latest/Tutorials/Part-1) show how to **start** your development and build your application.
* [DDD Implementation Guide](https://docs.abp.io/en/abp/latest/Domain-Driven-Design-Implementation-Guide) deep dives into Domain Driven Design if you want to go further.

### Common Libraries

Which **libraries** you should use to implement **common requirements**? Software development ecosystem is highly dynamic and it is hard to follow the latest tools, libraries and approaches.

...