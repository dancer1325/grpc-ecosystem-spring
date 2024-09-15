# gRPC-Java Flavors

* supported java based grpc-implementations
  * additional dependencies / grpc java flavor

| Flavor | Server | Client |
| ---  | --- | --- |
| [grpc-java](https://github.com/grpc/grpc-java/) | ✔️ | ✔️ |
| [Reactive gRPC (Reactor)](https://github.com/salesforce/reactive-grpc/tree/master/reactor) | ✔️ | ✏️ |
| [Reactive gRPC (RxJava)](https://github.com/salesforce/reactive-grpc/tree/master/rx-java) | ✔️ | ✏️ |
| [grpc-kotlin](https://github.com/grpc/grpc-kotlin) | ✔️ | ✏️ |
| [ScalaPB](https://scalapb.github.io/grpc.html) | ✔️ | ✏️ |
| [akka-grpc](https://github.com/akka/akka-grpc) | ✔️ | ✏️ |
| ... | ✔️ | ✏️ |

*✔️ = Build-in support* |
*✏️ = Requires customization*

## Server side

* NO additional configuration
* add `@GrpcService` | your implementation of the generated `BindableService`
* [Server - Getting Started](server/getting-started.md)

## Client side

* requirements
  * `StubFactory` / type of stub
* built-in stub factory beans | this library
  * gRPC-Java
    * `AbstractAsyncStub` -> `AsyncStubFactory`
    * `AbstractBlockingStub` -> `BlockingStubFactory`
    * `AbstractFutureStub` -> `FutureStubFactory`
* [Client - Getting Started](client/getting-started.md)
* [Client - Configuration - StubFactory](client/configuration.md#stubfactory)
