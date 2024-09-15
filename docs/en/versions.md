# Versions

[<- Back to Index](index.md)

* goal
  * Versioning 
    * Policy
    * lifecycles

## Table of Contents <!-- omit in toc -->

- [Versioning Policy](#versioning-policy)
- [Version Table](#version-table)
  - [Version 3.x](#version-3x)
  - [Version 2.x](#version-2x)
  - [Version 1.x](#version-1x)
  - [Upgrading Dependencies](#upgrading-dependencies)
  - [Release Notes](#release-notes)

## Versioning Policy

* major version of this project == spring-boot major version / compatible with
  * 1.x.x
    * versions are EOL and won't receive any updates
  * 2.x.x
    * current version
    * if there are spring-boot or gRPC releases -> will be updated
  * 3.x.x
    * current version
    * if there are spring-boot or gRPC releases -> will be updated
* minor version == feature version
  * if we bump spring-boot's or gRPC's version -> increment our feature version
  * if we add/change major features -> increment our feature version
  * you will NOT get any incompatibilities by upgrading
    * except, you use advanced features
* patch versions
  * normally, NOT release, but include these patches | next release

## Version Table

* spring & gRPC version
  * gRPC API version more frequently
  * if you are using the non-shaded netty + related libraries -> stick **exactly** to the version [documented](https://github.com/grpc/grpc-java/blob/master/SECURITY.md#netty) by gRPC

### Version 3.x

Current version.

| Version | spring-boot | spring-cloud |  gRPC  |      Date |
|:-------:|:-----------:|:------------:|:------:|----------:|
|  3.1.0  |    3.2.4    |   2023.0.0   | 1.63.0 | Apr, 2024 |
|  3.0.0  |    3.2.2    |   2023.0.0   | 1.60.1 | Feb, 2024 |

(\* Future versions)

* listed dependency versions -- are ONLY used -- | build process
* `grpc-spring-boot-starter` -- is usually -- backward or further ward compatible

### Version 2.x

| Version | spring-boot | spring-cloud |  gRPC  |      Date |
|:-------:|:-----------:| :----------: |:------:|----------:|
| 2.15.0  |   2.7.16    |   2021.0.8   | 1.58.0 | Sep, 2023 |
| 2.14.0  |   2.6.13    |   2021.0.5   | 1.51.0 | Nov, 2022 |
| 2.13.1  |    2.5.8    |   2020.0.5   | 1.42.2 | Jan, 2022 |
| 2.13.0  |    2.5.7    |   2020.0.4   | 1.42.1 | Nov, 2021 |
| 2.12.0  |    2.4.5    |   2020.0.2   | 1.37.0 | Mai, 2021 |
| 2.11.0  |    2.3.8    |    Hoxton    | 1.35.0 | Feb, 2021 |
| 2.10.1  |    2.3.3    |    Hoxton    | 1.31.1 | Aug, 2020 |
| 2.10.0  |    2.3.3    |    Hoxton    | 1.31.1 | Aug, 2020 |
|  2.9.0  |    2.3.1    |    Hoxton    | 1.30.0 | Jun, 2020 |
|  2.8.0  |    2.2.7    |    Hoxton    | 1.29.0 | Jun, 2020 |
|  2.7.0  |    2.2.4    |    Hoxton    | 1.27.1 | Feb, 2020 |
|  2.6.2  |    2.2.1    |    Hoxton    | 1.25.0 | Jan, 2020 |
|  2.6.1  |    2.2.1    |    Hoxton    | 1.25.0 | Nov, 2019 |
|  2.6.0  |    2.2.1    |    Hoxton    | 1.24.2 | Nov, 2019 |
|  2.5.1  |    2.1.6    |  Greenwich   | 1.22.2 | Aug, 2019 |
|  2.5.0  |    2.1.6    |  Greenwich   | 1.22.1 | Aug, 2019 |
|  2.4.0  |    2.1.5    |   Finchley   | 1.20.0 | Jun, 2019 |
|  2.3.0  |    2.1.4    |   Finchley   | 1.18.0 | Apr, 2019 |
|  2.2.1  |    2.0.7    |      ?       | 1.17.1 | Jan, 2019 |
|  2.2.0  |    2.0.6    |      ?       | 1.17.1 | Dec, 2018 |
|  2.1.0  |    2.0.?    |      ?       | 1.14.0 | Oct, 2018 |
|  2.0.1  |    2.0.?    |      ?       | 1.14.0 | Aug, 2018 |
|  2.0.0  |    2.0.?    |      ?       | 1.13.1 | Aug, 2018 |

### Version 1.x

End of life - No more updates planned.

| Version | spring-boot |  gRPC  |      Date |
| :-----: | :---------: | :----: | --------: |
|  1.4.2  |    1.?.?    | 1.12.0 | Jun, 2019 |
|  1.4.1  |    1.?.?    | 1.12.0 | Jun, 2018 |
|   ...   |    1.?.?    |  N/A   |

### Upgrading Dependencies

* if you upgrade ANY of the versions -> recommended doing -- via a -- bom
  * [spring-boot](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-parent)
  * [grpc-java](https://mvnrepository.com/artifact/io.grpc/grpc-bom)

### Release Notes

* check release notes on the changes / version
  * [grpc-spring-boot-starter](https://github.com/grpc-ecosystem/grpc-spring/releases)
  * [spring-boot](https://github.com/spring-projects/spring-boot/releases)
  * [grpc-java](https://github.com/grpc/grpc-java/releases)

---

[<- Back to Index](index.md)
