# Microservice Solution: Mobile Applications

The ABP Studio microservice solution template comes with an optional mobile application that is completely integrated to the solution. There are two options for the mobile application:

* MAUI
* React Native

You can select the mobile application type while [creating your solution](../../quick-starts/microservice.md).

## Fundamental Structures

The following sections explain the common structure of the mobile applications (valid for both of MAUI and React Native applications).

### The Mobile Gateway

If you've selected to include the mobile application into your solution, an API Gateway, named `MobileGateway` is also added to the solution. It is located under the `gateways/mobile` in the solution folder.

You can refer to the *[API Gateways](api-gateways.md)* document to understand the structure of the mobile gateway.

### Authentication

TODO

### User Management

TODO

### Tenant Management

TODO

### Profile Management

TODO

### Other Features

#### Feature 1

TODO

#### Feature 2

TODO

## Applications

Following sections explain the structure of MAUI and React Native Applications.

### The MAUI Application

This is the mobile application that is built based on Microsoft's [MAUI framework](https://learn.microsoft.com/en-us/dotnet/maui). It will be in the solution only if you've selected the MAUI as your mobile application option.

TODO: Project structure, running the application, debugging, development hints, link to the [tutorial](https://docs.abp.io/en/commercial/latest/tutorials/book-store/mobile/maui) etc.

### The React Native Application

This is the mobile application that is built based on Microsoft's [MAUI framework](https://learn.microsoft.com/en-us/dotnet/maui). It will be in the solution only if you've selected the MAUI as your mobile application option.

TODO: Project structure, running the application, debugging, development hints, link to the [tutorial](https://docs.abp.io/en/commercial/latest/tutorials/book-store/mobile/react-native) etc.