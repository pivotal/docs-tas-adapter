# Differences between Buildpacks in Tanzu Application Service and Application Service Adapter

This topic describes differences between the buildpack systems of Tanzu Application Service and Application Service Adapter that application developers should keep in mind when moving applications between the two platforms.

## Buildpack Overview

Application platforms such as Tanzu Application Service and Tanzu Application Platform use buildpacks to transform application source code and other assets into a self-contained artifact, such as a Cloud Foundry droplet or an Open Container Initiative container image. The platform then deploys this artifact to run instances of the application.

Tanzu Application Service uses Cloud Foundry buildpacks and provides a collection of system buildpacks that process many common languages and frameworks for cloud-native web applications. For more information about Cloud Foundry buildpacks, see the [How buildpacks work](https://docs.pivotal.io/application-service/buildpacks/understand-buildpacks.html) topic in the Tanzu Application Service documentation.

Tanzu Application Platform instead uses Tanzu Buildpacks, which implement the newer Cloud Native Buildpack specification, to process application source code. For more information about Cloud Native Buildpacks, see the [Cloud Native Buildpacks project website](https://buildpacks.io/).

An installation of Tanzu Application Platform provides a default collection of Tanzu Buildpacks, and Application Service Adapter uses this same set of Tanzu Buildpacks to stage Cloud Foundry applications. While this collection of Tanzu Buildpacks processes many of the same application languages and frameworks that the Tanzu Application Service system buildpacks do, there are some differences in the set of languages it can process, as well as differences in how certain buildpacks detect application code or accept configuration parameters.


## System Buildpack Comparison

Tanzu Application Service provides the following system buildpacks for Linux-based applications:

- [Binary buildpack](https://docs.pivotal.io/application-service/buildpacks/binary/index.html)
- [Go buildpack](https://docs.pivotal.io/application-service/buildpacks/go/index.html)
- [Java buildpack](https://docs.pivotal.io/application-service/buildpacks/java/index.html)
- [.NET Core buildpack](https://docs.pivotal.io/application-service/buildpacks/dotnet-core/index.html)
- [NGINX buildpack](https://docs.pivotal.io/application-service/buildpacks/nginx/index.html)
- [Node.js buildpack](https://docs.pivotal.io/application-service/buildpacks/node/index.html)
- [PHP buildpack](https://docs.pivotal.io/application-service/buildpacks/php/index.html)
- [Python buildpack](https://docs.pivotal.io/application-service/buildpacks/python/index.html)
- [R buildpack](https://docs.pivotal.io/application-service/buildpacks/r/index.html)
- [Ruby buildpack](https://docs.pivotal.io/application-service/buildpacks/ruby/index.html)
- [Staticfile buildpack](https://docs.pivotal.io/application-service/buildpacks/staticfile/index.html)

For many applications using these buildpacks on Tanzu Application Service, the suite of Tanzu Buildpacks contains a cloud-native buildpack that can process its language, frameworks, and build configuration. Tanzu Buildpacks are not yet available for R, Ruby, and Staticfile applications, although community-based solutions may be available.

### Binary Buildpack

Applications that use the [binary buildpack](https://docs.pivotal.io/application-service/buildpacks/binary/index.html) in Tanzu Application Service should instead use the [Tanzu Procfile Buildpack](https://docs.pivotal.io/tanzu-buildpacks/procfile/procfile-buildpack.html) when running via Application Service Adapter. Applications should include a Procfile that defines the process types and their start commands if they do not already.

### Go Buildpack

Applications that use the [Go buildpack](https://docs.pivotal.io/application-service/buildpacks/go/index.html) in Tanzu Application Service should instead use the [Tanzu Go Buildpack](https://docs.pivotal.io/tanzu-buildpacks/go/go-buildpack.html) when running via Application Service Adapter.

### Java Buildpack

Applications that use the [Java buildpack](https://docs.pivotal.io/application-service/buildpacks/java/index.html) in Tanzu Application Service should instead use the [Tanzu Java Buildpack](https://docs.pivotal.io/tanzu-buildpacks/java/java-buildpack.html) or the [Tanzu Java Native Image Buildpack](https://docs.pivotal.io/tanzu-buildpacks/java-native-image/java-native-image-buildpack.html) when running via Application Service Adapter.

### .NET Core Buildpack

Applications that use the [.NET Core buildpack](https://docs.pivotal.io/application-service/buildpacks/dotnet-core/index.html) in Tanzu Application Service should instead use the [Tanzu .NET Core Buildpack](https://docs.pivotal.io/tanzu-buildpacks/dotnet-core/dotnet-core-buildpack.html) when running via Application Service Adapter. For more information about migrating an application to use this buildpack, see [Migrating to the Tanzu .NET Core Buildpack](https://docs.pivotal.io/tanzu-buildpacks/dotnet-core/dotnet-core-migration.html) topic in the Tanzu Buildpacks documentation.

### NGINX Buildpack

Applications that use the [NGINX buildpack](https://docs.pivotal.io/application-service/buildpacks/nginx/index.html) in Tanzu Application Service should instead use the [Tanzu NGINX Buildpack](https://docs.pivotal.io/tanzu-buildpacks/nginx/nginx-buildpack.html) when running via Application Service Adapter.

### Node.js Buildpack

Applications that use the [Node.js buildpack](https://docs.pivotal.io/application-service/buildpacks/nodejs/index.html) in Tanzu Application Service should instead use the [Tanzu Node.js Buildpack](https://docs.pivotal.io/tanzu-buildpacks/nodejs/nodejs-buildpack.html) when running via Application Service Adapter. For more information about migrating an application to use this buildpack, see [Migrating to the Tanzu Node.js Buildpack](https://docs.pivotal.io/tanzu-buildpacks/nodejs/nodejs-migration.html) topic in the Tanzu Buildpacks documentation.

### PHP Buildpack

Applications that use the [PHP buildpack](https://docs.pivotal.io/application-service/buildpacks/php/index.html) in Tanzu Application Service should instead use the [Tanzu PHP Buildpack](https://docs.pivotal.io/tanzu-buildpacks/php/php-buildpack.html) when running via Application Service Adapter. For more information about migrating an application to use this buildpack, see [Migrating to the Tanzu PHP Buildpack](https://docs.pivotal.io/tanzu-buildpacks/php/php-migration.html) topic in the Tanzu Buildpacks documentation.

### Python Buildpack

Applications that use the [Python buildpack](https://docs.pivotal.io/application-service/buildpacks/python/index.html) in Tanzu Application Service should instead use the [Tanzu Python Buildpack](https://docs.pivotal.io/tanzu-buildpacks/python/python-buildpack.html) when running via Application Service Adapter.
