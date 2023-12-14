# ABP Studio Microservice Solution: Web Applications

## AuthServer

`Acme.CloudCrm.AuthServer` is the authentication server of the system. It is a single-sign-on point, which means all the applications are using it for user login. Once users login via an application, they don't need to enter credentials (username, password) again for the other applications in the same browser, until they logout from one of the applications.

The `AuthServer` application is also used by microservices as the Authority for JWT Bearer Authentication.

That application is mainly based on the [OpenIddict](../../../modules/openiddict.md), the [Identity](../../../modules/identity.md), and the [Account](../../../modules/account.md) modules. So, it basically has login, register, forgot password, two factor authentication and other authentication related pages.