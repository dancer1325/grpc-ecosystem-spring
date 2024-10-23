# Getting Started

[<- Back to Index](../index.md)

* goal
  * steps to convert your application -> grpc-spring-boot-starter

## Table of Contents <!-- omit in toc -->

- [Project Setup](#project-setup)
- [Dependencies](#dependencies)
  - [Interface-Project](#interface-project)
  - [Server-Project](#server-project)
  - [Client-Project](#client-project)
- [Creating the gRPC-Service Definitions](#creating-the-grpc-service-definitions)
- [Implementing the Service](#implementing-the-service)

## Additional Topics <!-- omit in toc -->

- *Getting started*
- [Configuration](configuration.md)
- [Exception Handling](exception-handling.md)
- [Contextual Data / Scoped Beans](contextual-data.md)
- [Testing the Service](testing.md)
- [Server Events](events.md)
- [Security](security.md)

## Project Setup

![project setup](/docs/assets/images/server-project-setup.svg)

* recommendation
  * ⭐split your project -- into -- 2-3 separate modules ⭐	
    * **interface project**
      * == raw protobuf files & generates the java model and service classes
    * **server project**
      * == actual implementation of your project / interface project -- is used as -- dependency
    * **client projects**
      * optional & >= 1 can exist
      * access -- via the pre-generated stubs, to the -- server

## Dependencies

### Interface-Project

#### Maven (Interface)

````xml
    <properties>
        <protobuf.version>3.23.4</protobuf.version>
        <protobuf-plugin.version>0.6.1</protobuf-plugin.version>
        <grpc.version>1.58.0</grpc.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>io.grpc</groupId>
            <artifactId>grpc-stub</artifactId>
            <version>${grpc.version}</version>
        </dependency>
        <dependency>
            <groupId>io.grpc</groupId>
            <artifactId>grpc-protobuf</artifactId>
            <version>${grpc.version}</version>
        </dependency>
        <dependency>
            <!-- Java 9+ compatibility - Do NOT update to 2.0.0 -->
            <groupId>jakarta.annotation</groupId>
            <artifactId>jakarta.annotation-api</artifactId>
            <version>1.3.5</version>
            <optional>true</optional>
        </dependency>
    </dependencies>

    <build>
        <extensions>
            <extension>
                <groupId>kr.motd.maven</groupId>
                <artifactId>os-maven-plugin</artifactId>
                <version>1.7.0</version>
            </extension>
        </extensions>

        <plugins>
            <plugin>
                <groupId>org.xolstice.maven.plugins</groupId>
                <artifactId>protobuf-maven-plugin</artifactId>
                <version>${protobuf-plugin.version}</version>
                <configuration>
                    <protocArtifact>com.google.protobuf:protoc:${protobuf.version}:exe:${os.detected.classifier}</protocArtifact>
                    <pluginId>grpc-java</pluginId>
                    <pluginArtifact>io.grpc:protoc-gen-grpc-java:${grpc.version}:exe:${os.detected.classifier}</pluginArtifact>
                </configuration>
                <executions>
                    <execution>
                        <goals>
                            <goal>compile</goal>
                            <goal>compile-custom</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
````

#### Gradle (Interface)

````gradle
buildscript {
    ext {
        protobufVersion = '3.23.4'
        protobufPluginVersion = '0.8.18'
        grpcVersion = '1.58.0'
    }
}

plugins {
    id 'java-library'
    id 'com.google.protobuf' version "${protobufPluginVersion}"
}

repositories {
    mavenCentral()
}

dependencies {
    implementation "io.grpc:grpc-protobuf:${grpcVersion}"
    implementation "io.grpc:grpc-stub:${grpcVersion}"
    compileOnly 'jakarta.annotation:jakarta.annotation-api:1.3.5' // Java 9+ compatibility - Do NOT update to 2.0.0
}

protobuf {
    protoc {
        artifact = "com.google.protobuf:protoc:${protobufVersion}"
    }
    generatedFilesBaseDir = "$projectDir/src/generated"
    clean {
        delete generatedFilesBaseDir
    }
    plugins {
        grpc {
            artifact = "io.grpc:protoc-gen-grpc-java:${grpcVersion}"
        }
    }
    generateProtoTasks {
        all()*.plugins {
            grpc {}
        }
    }
}

// Optional
eclipse {
    classpath {
        file.beforeMerged { cp ->
            def generatedGrpcFolder = new org.gradle.plugins.ide.eclipse.model.SourceFolder('src/generated/main/grpc', null);
            generatedGrpcFolder.entryAttributes['ignore_optional_problems'] = 'true';
            cp.entries.add( generatedGrpcFolder );
            def generatedJavaFolder = new org.gradle.plugins.ide.eclipse.model.SourceFolder('src/generated/main/java', null);
            generatedJavaFolder.entryAttributes['ignore_optional_problems'] = 'true';
            cp.entries.add( generatedJavaFolder );
        }
    }
}

// Optional
idea {
    module {
        sourceDirs += file("src/generated/main/java")
        sourceDirs += file("src/generated/main/grpc")
        generatedSourceDirs += file("src/generated/main/java")
        generatedSourceDirs += file("src/generated/main/grpc")
    }
}
````

### Server-Project

#### Maven (Server)

````xml
    <dependencies>
        <dependency>
            <groupId>net.devh</groupId>
            <artifactId>grpc-server-spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>example</groupId>
            <artifactId>my-grpc-interface</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
````

#### Gradle (Server)

````gradle
apply plugin: 'org.springframework.boot'

dependencies {
    compile('org.springframework.boot:spring-boot-starter')
    compile('net.devh:grpc-server-spring-boot-starter')
    compile('my-example:my-grpc-interface')
}

buildscript {
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
    }
}

````

### Client-Project

* check [client getting started page](../client/getting-started.md#client-project)

## Creating the gRPC-Service Definitions

* place your `.proto` files | `src/main/proto`
  * check [protobuf docs](https://developers.google.com/protocol-buffers/docs/proto3)
  * _Example:_ [grpc-lib](../../../examples/grpc-lib)

    ````proto
    syntax = "proto3";
    
    package net.devh.boot.grpc.example;
    
    option java_multiple_files = true;
    option java_package = "net.devh.boot.grpc.examples.lib";
    option java_outer_classname = "HelloWorldProto";
    
    // The greeting service definition.
    service MyService {
        // Sends a greeting
        rpc SayHello (HelloRequest) returns (HelloReply) {
        }
    }
    
    // The request message containing the user's name.
    message HelloRequest {
        string name = 1;
    }
    
    // The response message containing the greetings
    message HelloReply {
        string message = 1;
    }
    ````

* maven/gradle protobuf plugins + [`protoc`](https://mvnrepository.com/artifact/com.google.protobuf/protoc) + [`protoc-gen-grpc-java`](https://mvnrepository.com/artifact/io.grpc/protoc-gen-grpc-java) plugin or others ([reactive-grpc](https://github.com/salesforce/reactive-grpc)) -> generate the
  * data classes,
  * grpc service `ImplBase`s and `Stub`s
    * `ImplBase` classes == base logic / dummy implementation -- is mapped to the -- grpc service methods
      * check [Implementing the service](#implementing-the-service)
    * `Stub` classes == complete client implementations
      * check [Getting the client started](../client/getting-started.md) 
  * other/additional classes -- depending on -- other plugins used

## Implementing the Service

* `protoc-gen-grpc-java` plugin
  * generates a class / used by grpc services
    * _Example:_ `MyServiceGrpc` & `MyService` == name of the grpc service | ".proto"
    * == client stubs + client server `ImplBase` / you will need to extend
* steps
  1. `MyServiceImpl` -- must extends -- `MyServiceGrpc.MyServiceImplBase`
  2. `@GrpcService` | `MyServiceImpl` class
  3. `MyServiceImpl` | application context,
     1. create `@Bean` | `@Configuration` classes or
     2. placing it | spring's automatically detected paths ( -- _Example:_ same or a sub package of your `Main` class --)
  4. implement the grpc service methods

* _Example:_ grpc service class

    ````java
    import example.HelloReply;
    import example.HelloRequest;
    import example.MyServiceGrpc;
    
    import io.grpc.stub.StreamObserver;
    
    import net.devh.boot.grpc.server.service.GrpcService;
    
    @GrpcService
    public class MyServiceImpl extends MyServiceGrpc.MyServiceImplBase {
    
        @Override
        public void sayHello(HelloRequest request, StreamObserver<HelloReply> responseObserver) {
            HelloReply reply = HelloReply.newBuilder()
                    .setMessage("Hello ==> " + request.getName())
                    .build();
            responseObserver.onNext(reply);
            responseObserver.onCompleted();
        }
    
    }
    ````

* TODO:
> **Note**: Theoretically it is not necessary to extend the `ImplBase` and instead implement `BindableService` yourself.
> However, doing so might result in bypassing spring security's checks.

That's all there is to that. Now you can start your spring-boot application and start sending requests to your
grpc-service.

By default, the grpc-server will be started on port `9090` using `PLAINTEXT` mode.

You can test that your application is working as expected by running these [gRPCurl](https://github.com/fullstorydev/grpcurl) commands:

````sh
grpcurl --plaintext localhost:9090 list
grpcurl --plaintext localhost:9090 list net.devh.boot.grpc.example.MyService
# Linux (Static content)
grpcurl --plaintext -d '{"name": "test"}' localhost:9090 net.devh.boot.grpc.example.MyService/sayHello
# Windows or Linux (dynamic content)
grpcurl --plaintext -d "{\"name\": \"test\"}" localhost:9090 net.devh.boot.grpc.example.MyService/sayHello
````

See [here](testing.md#grpcurl) for `gRPCurl` example command output and additional information.

> Note: Don't forget to write [actual/automated tests](testing.md) for your service implementation.

## Additional Topics <!-- omit in toc -->

- *Getting Started*
- [Configuration](configuration.md)
- [Exception Handling](exception-handling.md)
- [Contextual Data / Scoped Beans](contextual-data.md)
- [Testing the Service](testing.md)
- [Server Events](events.md)
- [Security](security.md)

----------

[<- Back to Index](../index.md)
