# Brave / Sleuth Support

[<- Back to Index](index.md)

* integration with [brave](https://github.com/openzipkin/brave) / [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth)

## Table of Contents <!-- omit in toc -->

- [Dependencies](#dependencies)
  - [Brave](#brave)
  - [Spring Cloud Sleuth](#spring-cloud-sleuth)
- [Opt-Out](#opt-out)
- [Additional Notes](#additional-notes)

## Dependencies

* grpc-spring-boot-starter provides automatic support for `Brave Instrumentation: GRPC`
* requirements
  1. dependencies for Brave | classpath
  2. `Tracing` bean | your application context
     1. if Spring Cloud Sleuth | your classpath -> it will automatically configure this bean 

### Brave

* via Maven

    ````xml
    <dependency>
        <groupId>io.zipkin.brave</groupId>
        <artifactId>brave-instrumentation-grpc</artifactId>
    </dependency>
    ````

* via Gradle

    ````groovy
    compile("io.zipkin.brave:brave-instrumentation-grpc")
    ````

### Spring Cloud Sleuth

* via Maven

    ````xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-sleuth</artifactId>
    </dependency>
    ````

* via Gradle

    ````groovy
    compile("org.springframework.cloud:spring-cloud-starter-sleuth")
    ````

* check [official documentation](https://spring.io/projects/spring-cloud-sleuth)

## Opt-Out

* `spring.sleuth.grpc.enabled=false`
  * opt-out -- from the -- grpc-tracing


## Additional Notes

* Spring-Cloud-Sleuth provides classes / -- exists -- for  compatibility reasons with a different library
  * _Example:_ [`SpringAwareManagedChannelBuilder`](https://javadoc.io/page/org.springframework.cloud/spring-cloud-sleuth-core/latest/org/springframework/cloud/sleuth/instrument/grpc/SpringAwareManagedChannelBuilder.html) 
  * \+ this library  -- ⚠️ NOT use ⚠️
* grpc-spring-boot-starter provides the same/extended functionality out of the box with
  * [`GrpcChannelFactory`](https://javadoc.io/page/net.devh/grpc-client-spring-boot-starter/latest/net/devh/boot/grpc/client/channelfactory/GrpcChannelFactory.html) + related classes
  * check [sleuth's javadoc note](https://github.com/spring-cloud/spring-cloud-sleuth/blob/59216c32f7848ec337fb68d1dbec8e87eeb6bf59/spring-cloud-sleuth-core/src/main/java/org/springframework/cloud/sleuth/instrument/grpc/SpringAwareManagedChannelBuilder.java#L31-L34)

----------

[<- Back to Index](index.md)
