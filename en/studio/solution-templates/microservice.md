# ABP Studio Microservice Solution Template

ABP Studio provides pre-architected and production-ready templates to jump start a new solution. One of them is the Microservice solution template. You can use it to build distributed systems with common microservice patterns. It includes multiple services, API gateways and applications that are well integrated to each other and ready to be a great base solution for your system.

## Overall

In this section, you will know what that solution template offers to you.

### Pre-Installed Libraries & Services

All the following **libraries and services** are **pre-installed** and **configured** for both of **development** and **production** environments. After creating your solution, you can **change** to **remove** most of them.

* **[Autofac](https://autofac.org/)** for [Dependency Injection](https://docs.abp.io/en/abp/latest/Dependency-Injection)
* **[Serilog](https://serilog.net/)** with File, Console and Elasticsearch [logging](https://docs.abp.io/en/abp/latest/Logging) providers
* **[Prometheus](https://prometheus.io/)** for collecting metrics
* **[Redis](https://redis.io/)** for [distributed caching](https://docs.abp.io/en/abp/latest/Caching) and [distributed locking](https://docs.abp.io/en/abp/latest/Distributed-Locking)
* **[Swagger](https://swagger.io/)** to explore and test REST APIs.

### Pre-Installed & Configured Features

* **Jwt Bearer Authentication** for microservices and applications.
* **[Permission](https://docs.abp.io/en/abp/latest/Authorization)** (authorization), **[setting](https://docs.abp.io/en/abp/latest/Settings)** and **[feature](https://docs.abp.io/en/abp/latest/Features)** management systems are pre-configured and ready to use.

### Options

Microservice startup template asks for some preferences while creating your solution.

#### Database Provider

There are two database provider options are provided on a new microservice solution creation:

* **[Entity Framework Core](https://docs.abp.io/en/abp/latest/Entity-Framework-Core)** with SQL Server, MySQL and PostgreSQL DBMS options. You can [switch to anther DBMS](https://docs.abp.io/en/abp/latest/Entity-Framework-Core-Other-DBMS) manually after creating your solution.
* **[MongoDB](https://docs.abp.io/en/abp/latest/MongoDB)**