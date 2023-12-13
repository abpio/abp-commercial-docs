# ABP Studio Microservice Solution Structure

This document explains the solution and folder structure of ABP Studio's [microservice solution template](index.md).

> This document assumes that you've created a new microservice solution by following the *[Quick Start: Creating a Microservice Solution with ABP Studio](../../quick-starts/microservice.md)* guide.

## Understanding the Modules

When you create a new microservice solution, you will see a tree structure similar to the one below in the *Solution Explorer* panel:

![microservice-solution-in-explorer](images/microservice-solution-in-explorer.png)

Each leaf item (e.g. `Acme.CloudCrm.IdentityService`, `Acme.CloudCrm.Web`, `Acme.CloudCrm.WebGateway`...) in the tree above is an **ABP Studio module**. An ABP Studio module can be a web application, an API gateway, a microservice, a console application or whatever .NET allows you to build. They are grouped into **solution folders** (`apps`, `gateways` and `services`) in that solution.

**Each ABP Studio module has a separate .NET solution**; this allows your team to develop them individually, in keeping with the nature of the microservices architecture.

> Refer to the *[Concepts](../../concepts.md)* document for a full definition of ABP Studio solution, module and package terms.

The next sections explains the purpose of these modules.

### AuthServer

`Acme.CloudCrm.AuthServer` is the authentication server of the system. It is a single-sign-on point, which means all the applications are using it for user login. Once users login via an application, they don't need to enter credentials (username, password) again for the other applications in the same browser, until they logout from one of the applications.

The `AuthServer` application is also used by microservices as the Authority for JWT Bearer Authentication.

That application is mainly based on the [OpenIddict](../../../modules/openiddict.md), the [Identity](../../../modules/identity.md), and the [Account](../../../modules/account.md) modules. So, it basically has login, register, forgot password, two factor authentication and other authentication related pages.

### Maui

This is the mobile application that is built based on Microsoft MAUI framework.