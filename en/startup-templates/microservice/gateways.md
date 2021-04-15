# Microservice Startup Template API Gateways

> API Gateways are used as single entry point to the microservices. Abp microservice startup template uses [Ocelot .Net Api Gateway](https://github.com/ThreeMammals/Ocelot) library. Through this library, gateways have the functionality of *routing*; *rate limiting*, *retry policies* etc. For more, check out [Ocelot Documentations](https://ocelot.readthedocs.io/en/latest/).

There are 3 different gateways are presented in the microservice startup template.

- **Web Gateway** is located under *gateways/web* folder. 
- **Internal Gateway** is located under *gateways/internal* folder. 
- **Public Web Gateway**  is located under *gateways/webpublic* folder. 

Add the test projects required by your needs under this folder. Check [The Test Projects Docs](https://docs.abp.io/en/abp/latest/Testing) for more information.

![overall-applications](../../images/overall-applications.png)

## Web Gateway



## Internal Gateway



## Public Web Gateway



## Data Seed



## Backend for Frontend Pattern (BFF)

Microservice template uses BFF pattern; defines a separate Api gateway for each application.