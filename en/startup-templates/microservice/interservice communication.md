# Communications Between Microservices

There are two basic messaging patterns for communication between microservices:

## Synchronous Communication

A microservice calls an other microservice exposed API by using a protocol like HTTP or RPC. Caller waits for response from receiver.

## Asynchronous Communication

A microservice sends messages without waiting for response and one or more microservices process the message. This is done by using a message broker or event bus.

## Next

- [Synchronous Communication](synchronous-inter-service communication.md)
- [Asynchronous Communication](asynchronous-inter-service communication.md)

