# Spring Boot Actuator Support

[<- Back to Index](index.md)

* integration with [Spring-Boot-Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-endpoints.html) / supported features
  * Client + server metrics
  * Server `InfoContributor`

## Table of Contents <!-- omit in toc -->

- [Dependencies](#dependencies)
- [Metrics](#metrics)
  - [Counter](#counter)
  - [Timer](#timer)
  - [Viewing the metrics](#viewing-the-metrics)
  - [Metric configuration](#metric-configuration)
- [InfoContributor](#infocontributor)
- [Opt-Out](#opt-out)

## Dependencies

* metric collection & other actuator features 👁️ are optional 👁️
* if a `MeterRegistry` is | application context -> enabled automatically
* way to configure
  * via Maven

    ````xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    
    <!-- if you want to view the metrics | resource / interpreted by browser 
    spring-boot-web runs | port / != grpc server port -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ````

  * via Gradle

    ````groovy
    compile("org.springframework.boot:spring-boot-starter-actuator")
    
    //if you want to view the metrics | resource / interpreted by browser
    //spring-boot-web runs | port / != grpc server port
    compile("org.springframework.boot:spring-boot-starter-web")
    ````

* ways to view metrics
  * JMX
  * browser -- through -- actuator

## Metrics

* grpc-spring-boot-starter will automatically configure
  * `ClientInterceptor`s / `ServerInterceptor`s will gather the metrics

### Counter

- `grpc.client.requests.sent`: total # of requests sent
- `grpc.client.responses.received`: total # of responses received
- `grpc.server.requests.received`: total # of requests received
- `grpc.server.responses.sent`: total # of responses sent

**Tags:**

- `service`: requested grpc service name -- via -- protobuf name
- `method`: requested grpc method name -- via -- protobuf name
- `methodType`: type of the requested grpc method

### Timer

- `grpc.client.processing.duration`: total time / taken for the client -- to -- complete the call (including network delay)
- `grpc.server.processing.duration`: total time / taken for the server -- to -- complete the call

**Tags:**

- `service`: The requested grpc service name (using protobuf name)
- `method`: The requested grpc method name (using protobuf name)
- `methodType`: The type of the requested grpc method.
- `statusCode`: Response `Status.Code`

## gRPC A66 Metrics

* additional to above listed metrics
* grpc-spring-boot-starter will automatically configure / gather [gRPC A66](https://github.com/grpc/proposal/blob/master/A66-otel-stats.md) metrics

### Client Metrics

* TODO:
### Counter

- `grpc.client.attempt.started`: The total number of RPC attempts started, including those that have not completed.
  - Tags: `grpc.method`

### Distribution

- `grpc.client.attempt.sent_total_compressed_message_size`: Total bytes (compressed but not encrypted) sent across all request messages (metadata excluded) per RPC attempt.
  - Tags: `grpc.method`, `grpc.status`
- `grpc.client.attempt.rcvd_total_compressed_message_size`: Total bytes (compressed but not encrypted) received across all response messages (metadata excluded) per RPC attempt.
  - Tags: `grpc.method`, `grpc.status`

### Timer

- `grpc.client.attempt.duration`: The total time taken to complete an RPC attempt including the time it takes to pick a sub channel.
  - Tags: `grpc.method`, `grpc.status`
- `grpc.client.call.duration`: The total time taken by gRPC library to complete an RPC from the application’s perspective.
  - Tags: `grpc.method`, `grpc.status`

### Server Metrics

### Counter

- `grpc.server.call.started`: The total number of RPCs started, including those that have not completed.
  - Tags: `grpc.method`

### Distribution

- `grpc.server.call.sent_total_compressed_message_size`: Total bytes (compressed but not encrypted) sent across all response messages (metadata excluded) per RPC.
  - Tags: `grpc.method`, `grpc.status`
- `grpc.server.call.rcvd_total_compressed_message_size`: Total bytes (compressed but not encrypted) received across all request messages (metadata excluded) per RPC.
  - Tags: `grpc.method`, `grpc.status`

### Timer

- `grpc.server.call.duration`: The total time an RPC takes from the server transport’s perspective.
  - Tags: `grpc.method`, `grpc.status`

### Viewing the metrics

You can view the grpc metrics along with your other metrics at `/actuator/metrics` (requires a web-server) or via JMX.

> **Note:** You might have to enable your metrics endpoint first.
>
> ````properties
> management.endpoints.web.exposure.include=metrics
> #management.endpoints.jmx.exposure.include=metrics
> management.endpoint.metrics.enabled=true
> ````

Read the official documentation for more information about
[Spring Boot Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-endpoints.html).

### Metric configuration

By default, the client will only create metrics for calls that have been made. However, the server will try to find all
registered services and initialize metrics for them.

You can customize the behavior by overwriting the beans. The following demonstrates this using the
`MetricCollectingClientInterceptor`:

````java
@Bean
MetricCollectingClientInterceptor metricCollectingClientInterceptor(MeterRegistry registry) {
    MetricCollectingClientInterceptor collector = new MetricCollectingClientInterceptor(registry,
            counter -> counter.tag("app", "myApp"), // Customize the Counters
            timer -> timer.tag("app", "myApp"), // Customize the Timers
            Code.OK, Code.INVALID_ARGUMENT, Code.UNAUTHENTICATED); // Eagerly initialized status codes
    // Pre-generate metrics for some services (to avoid missing metrics after restarts)
    collector.preregisterService(MyServiceGrpc.getServiceDescriptor());
    return collector;
}
````

## InfoContributor

*(Server only)*

The server part automatically configures an
[`InfoContributor`](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/actuate/info/InfoContributor.html)
that publishes the following information:

- `grpc.server`:
  - `port`: The grpc server port
  - `services`: A list of grpc-services
    - With their methods

You can view the grpc info along with your other info at `/actuator/info` (requires a web-server) or via JMX.

> **Note:** You might have to enable your info endpoint first.
>
> ````properties
> management.endpoints.web.exposure.include=info
> #management.endpoints.jmx.exposure.include=info
> management.endpoint.info.enabled=true
> ````

You can turn of the service listing (for both actuator and grpc) using `grpc.server.reflectionServiceEnabled=false`.

## Opt-Out

You can opt out from the actuator autoconfiguration using the following annotation:

````java
@EnableAutoConfiguration(exclude = {GrpcClientMetricAutoConfiguration.class, GrpcServerMetricAutoConfiguration.class})
````

or using properties:

````properties
spring.autoconfigure.exclude=\
net.devh.boot.grpc.client.autoconfigure.GrpcClientMetricAutoConfiguration,\
net.devh.boot.grpc.server.autoconfigure.GrpcServerMetricAutoConfiguration
````

You can opt out from collecting gRPC A66 metrics using `grpc.metricsA66Enabled=false`.

----------

[<- Back to Index](index.md)
