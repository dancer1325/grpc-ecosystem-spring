# Survey - gRPC/Spring

* [survey](https://docs.google.com/forms/d/e/1FAIpQLSfHgvh_Z0_wwX7JQLERanJ-AAXjiKh23_kSI3Rl5mnKVQ8Bpw/viewform?resourcekey=0-mEilI6lFvIfVXiUniEyCog) to get feedback

# gRPC Spring Boot Starter

[![Build master branch](https://github.com/grpc-ecosystem/grpc-spring/workflows/Build%20master%20branch/badge.svg)](https://github.com/grpc-ecosystem/grpc-spring/actions)
[![Maven Central with version prefix filter](https://img.shields.io/maven-central/v/net.devh/grpc-spring-boot-starter.svg)](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22net.devh%22%20grpc)
[![License](https://img.shields.io/github/license/mashape/apistatus.svg)](LICENSE)
[![Crowdin](https://badges.crowdin.net/grpc-spring-boot-starter/localized.svg)](https://crowdin.com/project/grpc-spring-boot-starter)

[![Client-Javadoc](https://www.javadoc.io/badge/net.devh/grpc-client-spring-boot-starter.svg?label=Client-Javadoc)](https://www.javadoc.io/doc/net.devh/grpc-client-spring-boot-starter)
[![Server-Javadoc](https://www.javadoc.io/badge/net.devh/grpc-server-spring-boot-starter.svg?label=Server-Javadoc)](https://www.javadoc.io/doc/net.devh/grpc-server-spring-boot-starter)
[![Common-Javadoc](https://www.javadoc.io/badge/net.devh/grpc-common-spring-boot.svg?label=Common-Javadoc)](https://www.javadoc.io/doc/net.devh/grpc-common-spring-boot)

README: [English](README.md) | [中文](README-zh-CN.md)

**Documentation:** [English](https://grpc-ecosystem.github.io/grpc-spring/en/) | [中文](https://grpc-ecosystem.github.io/grpc-spring/zh-CN/)

## Features

* Automatically
  * configures and runs the gRPC server -- with your -- `@GrpcService` implementations
  * creates and manages your grpc channels and stubs with `@GrpcClient`
* Supports
  * other grpc-java flavors
    * [Reactive gRPC (RxJava)](https://github.com/salesforce/reactive-grpc/tree/master/rx-java)
    * [grpc-kotlin](https://github.com/grpc/grpc-kotlin), ...)
    * Server-side work for ALL grpc-java flavors 
      * `io.grpc.BindableService` based
    * Client-side requires custom `StubFactory`s / built-in support
      * grpc-java
      * others ❓
  * [Spring-Security](https://github.com/spring-projects/spring-security)
  * [Spring Cloud](https://spring.io/projects/spring-cloud)
    * Server-side: Adds grpc-port information to the service registration details\
      Currently natively supported:
      * [Consul](https://github.com/spring-cloud/spring-cloud-consul)
      * [Eureka](https://github.com/spring-cloud/spring-cloud-netflix)
      * [Nacos](https://github.com/spring-cloud-incubator/spring-cloud-alibaba)
      * (Please report missing ones, so we can add support for them)
    * Client-side: Reads the service's target addresses from spring's `DiscoveryClient` (all flavors)
  * [Spring Sleuth](https://github.com/spring-cloud/spring-cloud-sleuth) -- as -- distributed tracing solution
    * requirements
      * [brave-instrumentation-grpc](https://mvnrepository.com/artifact/io.zipkin.brave/brave-instrumentation-grpc) present
  * global and custom gRPC server/client interceptors
* Automatic metric support -- [micrometer](https://micrometer.io/)/[actuator](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-project/spring-boot-actuator) based
* Also works with (non-shaded) grpc-netty

## Versions

* latest version `3.1.0.RELEASE`
  * compiled with -- spring-boot `3.2.4` & spring-cloud `2023.0.0`
  * ALSO compatible with a large variety of other versions
* overview of all versions & their respective library versions 
  * check [documentation](https://yidongnan.github.io/grpc-spring-boot-starter/en/versions.html)
* ⚠️This project can also be used WITHOUT Spring-Boot ⚠️
  * -> manual bean configuration

## Usage

### gRPC Server + Client

* via Maven

    ````xml
    <dependency>
      <groupId>net.devh</groupId>
      <artifactId>grpc-spring-boot-starter</artifactId>
      <version>3.1.0.RELEASE</version>
    </dependency>
    ````

* via Gradle

    ````gradle
    dependencies {
      implementation 'net.devh:grpc-spring-boot-starter:3.1.0.RELEASE'
    }
    ````

### gRPC Server

* via Maven

    ````xml
    <dependency>
      <groupId>net.devh</groupId>
      <artifactId>grpc-server-spring-boot-starter</artifactId>
      <version>3.1.0.RELEASE</version>
    </dependency>
    ````

* via Gradle

    ````gradle
    dependencies {
      implementation 'net.devh:grpc-server-spring-boot-starter:3.1.0.RELEASE'
    }
    ````

* ``@GrpcService`` | server interface implementation(s) 

````java
@GrpcService
public class GrpcServerService extends GreeterGrpc.GreeterImplBase {

    @Override
    public void sayHello(HelloRequest req, StreamObserver<HelloReply> responseObserver) {
        HelloReply reply = HelloReply.newBuilder().setMessage("Hello ==> " + req.getName()).build();
        responseObserver.onNext(reply);
        responseObserver.onCompleted();
    }

}
````

* [settings](grpc-server-spring-boot-starter/src/main/java/net/devh/boot/grpc/server/config/GrpcServerProperties.java)
  * -- can be changed via -- Spring's property mechanism 
  * `grpc.server.*`
    * grpc Server settings
    * default port `9090`
* [documentation](https://yidongnan.github.io/grpc-spring-boot-starter/) 

### gRPC Client

* via Maven

    ````xml
    <dependency>
      <groupId>net.devh</groupId>
      <artifactId>grpc-client-spring-boot-starter</artifactId>
      <version>3.1.0.RELEASE</version>
    </dependency>
    ````

* via Gradle

    ````gradle
    dependencies {
      compile 'net.devh:grpc-client-spring-boot-starter:3.1.0.RELEASE'
    }
    ````

* `@GrpcClient(serverName)` | grpc client stub's field
  * ⚠️NOT used + `@Autowired` or `@Inject` ⚠️

  ````java
  @GrpcClient("gRPC server name")
  private GreeterGrpc.GreeterBlockingStub greeterStub;
  ````

* SAME grpc server name / SEVERAL channels & different stubs (even with different
interceptors)
* ways to send queries | your server

    ````java
    HelloReply response = stub.sayHello(HelloRequest.newBuilder().setName(name).build());
    ````

* [settings](grpc-client-spring-boot-starter/src/main/java/net/devh/boot/grpc/client/config/GrpcChannelProperties.java)
  * can be changed -- via -- Spring's property mechanism
  * `grpc.client.(serverName).` prefix
  * such as
    * target address / client individually
    * default url mapping -- via -- `NameResolver.Factory` beans
      * if you don't configure that bean -> default uri == default scheme + default name
        * _Example:_ `dns:/<name>`
* [documentation](https://yidongnan.github.io/grpc-spring-boot-starter/)

## Running with (non-shaded) grpc-netty

* library supports 
  * `grpc-netty` 
  * `grpc-netty-shaded`
    * -- might prevent conflicts --
      * with incompatible grpc-versions
      * between libraries / require different versions of netty
    * if it is present | classpath -> 👁️library will always favor it over the non-shaded grpc-netty one 👁️	

* via Maven

    ````xml
    <dependency>
        <groupId>io.grpc</groupId>
        <artifactId>grpc-netty</artifactId>
        <version>${grpcVersion}</version>
    </dependency>
    
    <!-- For both -->
    <dependency>
        <groupId>net.devh</groupId>
        <artifactId>grpc-spring-boot-starter</artifactId>
        <version>...</version>
        <exclusions>
            <exclusion>
                <groupId>io.grpc</groupId>
                <artifactId>grpc-netty-shaded</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <!-- For the server (only) -->
    <dependency>
        <groupId>net.devh</groupId>
        <artifactId>grpc-server-spring-boot-starter</artifactId>
        <version>...</version>
        <exclusions>
            <exclusion>
                <groupId>io.grpc</groupId>
                <artifactId>grpc-netty-shaded</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <!-- For the client (only) -->
    <dependency>
        <groupId>net.devh</groupId>
        <artifactId>grpc-client-spring-boot-starter</artifactId>
        <version>...</version>
        <exclusions>
            <exclusion>
                <groupId>io.grpc</groupId>
                <artifactId>grpc-netty-shaded</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    ````

* via Gradle

    ````groovy
    implementation "io.grpc:grpc-netty:${grpcVersion}"
    
    implementation 'net.devh:grpc-spring-boot-starter:...' exclude group: 'io.grpc', module: 'grpc-netty-shaded' // For both
    implementation 'net.devh:grpc-client-spring-boot-starter:...' exclude group: 'io.grpc', module: 'grpc-netty-shaded' // For the client (only)
    implementation 'net.devh:grpc-server-spring-boot-starter:...' exclude group: 'io.grpc', module: 'grpc-netty-shaded' // For the server (only)
    ````

## Example-Projects

* [examples](examples)

## Troubleshooting

* [documentation](https://yidongnan.github.io/grpc-spring-boot-starter/en/trouble-shooting) 

## Contributing

* [CONTRIBUTING.md](CONTRIBUTING.md) 
