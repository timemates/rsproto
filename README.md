![Maven metadata URL](https://img.shields.io/maven-metadata/v?metadataUrl=https%3A%2F%2Fmaven.timemates.org%2Freleases%2Forg%2Ftimemates%2Frsproto%2Fclient-core%2Fmaven-metadata.xml)
![GitHub issues](https://img.shields.io/github/issues/timemates/rsproto)
![GitHub closed pull requests](https://img.shields.io/github/issues-pr-closed/timemates/rsproto)
![GitHub License](https://img.shields.io/github/license/timemates/rsproto)
# RSProto
RSProto is a framework that designed to provide ability to expose your API as RPC Services. It facilitates the creation of gRPC-like services from .proto files through code generation. The framework also provides essential core components for both server and client.

> **Warning** <br>
> This project is experimental, be ready for bugs and possible changes.

## Features
- **Gradle Plugin**: `.proto` to RSocket code generator (both client and server).
- **Server Core** (JVM only): Interceptors, Instances API and bridge between Ktor and library.
- **Client Core** (JVM, Web, iOS): Metadata and basic interface-markers.

> **Known issues** <br>
> As RSocket does not natively support client-only streaming – it's not supported by the
> code-generator.

## Getting Started
First of all, you should have the following repository:
```kotlin
repositories {
    maven("https://maven.timemates.org/releases")
}
```

### Core components
Before using builtin generation from `.proto` to RSocket services, you should add the following dependencies:
```kotlin
dependencies {
    // for server
    implementation("org.timemates.rsproto:server-core:$version")
    // for client
    commonMainImplementation("org.timemates.rsproto:client-core:$version")

    // for server & client
    commonMainImplementation("org.timemates.rsproto:common-core:$version")
}
```
#### Server initialization
To configure your server, you can use ready-to-use build-bridges between Ktor and RSProto:
```kotlin
fun Application.configureServer() {
    routing {
        rSocketServer("/rsocket") {
            interceptor(MyInterceptor())
            service(MyService())

            instances {
                protobuf { 
                    encodeDefaults = true
                }
            }
        }
    }
}
```
#### Client initialization
Clients are generated by the code-generation Gradle plugin. To use them you need instance of `rsocket` and `protobuf`:
```kotlin
val apiService = TestServiceApi(rsocket, protobuf)
apiService.sayHello()
```

### Code-generation plugin
To implement code-generation plugin in your buildscript, add the following:
```kotlin
plugins {
    id("org.timemates.rsproto") version "$version"
}
```
And use it inside your buildscript:
```kotlin
rsproto {
    protoSourcePath.set("src/main/proto")
    generationOutputPath.set("generated/proto-generator/src/commonMain")
    clientGeneration.set(true)
    serverGeneration.set(true)
}
```

## Feedback

For bugs, questions and discussions please use
the [GitHub Issues](https://github.com/timemates/rsproto/issues).

## License

This library is licensed under [MIT License](LICENSE). Feel free to use, modify, and distribute it for any purpose.
