# Benchmarking

[<- Back to Index](index.md)

* NOT performance added to gRPC-java
* official gRPC benchmarks
  * [grpc.io: Benchmarking](https://grpc.io/docs/guides/benchmarking/)
  * [grpc-java: Running Benchmarks](https://github.com/grpc/grpc-java/tree/master/benchmarks#grpc-benchmarks)
* gRPC vs plain HTTP
  * Heavily optimized web-servers performance == normal gRPC-servers performance
  * Binary data format
    * -> faster, BUT NOT human readable
  * Protobuf defined data scheme
    * allows generating data classes and clients / MANY languages
  * HTTP/2 connections and connection pooling

----------

[<- Back to Index](index.md)
