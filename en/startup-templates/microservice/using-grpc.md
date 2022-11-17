# Using gRPC

[gRPC](https://grpc.io/) is a cross-platform open source high performance remote procedure call framework created by Google to be used to provide inter-communication between large number of microservices. It uses [HTTP/2](https://en.wikipedia.org/wiki/HTTP/2) for transport layer and [Protocol Buffers](https://en.wikipedia.org/wiki/Protocol_Buffers) as the data format to serialize the structured data. It generates cross-platform client and server bindings different languages with the features like authentication, bi-directional streaming and flow control, blocking or non-blocking bindings, cancellation and timeouts.

![grpc-overview](../../images/grpc-overview.png)

<div class="figure" style="text-align:center"><i>gRPC Overview from grpc.io</i></div>

## Benefits

- Designed for low latency and high throughput communication, gRPC is great for lightweight **microservices** where the efficiency is critical.

- gRPC services can push messages in real-time without polling. It's support for bi-directional streaming makes gRPC an excellent tool for point to point real-time communication.
- gRPC toolong supports many popular development languages, making gRPC a good choice for multi-language environments.
- Protubuf is a lightweight mesage format. A gRPC message is always smaller than an equivalent JSON message. That can be an important factor of choice where the network environment is constrained.
- gRPC can be used to communicate between apps on the same machine. For more information, see [Inter-process communication with gRPC](https://learn.microsoft.com/en-us/aspnet/core/grpc/interprocess?view=aspnetcore-7.0).

<div class="figure">See more at <a href="https://learn.microsoft.com/en-us/aspnet/core/grpc/comparison">Compare gRPC services with HTTP APIs</a></div>

## Using gRPC with the ABP Framework

There are two great articles about gRPC integration to the ABP framework by Halil Ibrahim Kalkan:

- [Using gRPC with the ABP Framework | ABP Community](https://community.abp.io/posts/using-grpc-with-the-abp-framework-2dgaxzw3)
- [Consuming gRPC Services from Blazor WebAssembly Application Using gRPC-Web | ABP Community](https://community.abp.io/posts/consuming-grpc-services-from-blazor-webassembly-application-using-grpcweb-dqjry3rv)

## Adding a gRPC service to a microservice

#TODO



## Consuming the added gRPC service from another microservice

#TODO



## Consuming gRPC services from the web application

#TODO



## Dealing with multi-tenancy, localization and authorization in gRPC service calls

#TODO