# Differences between buildpacks in TAS for VMs and Application Service Adapter

This topic describes the differences between the buildpack systems of Tanzu Application Service for VMs (TAS for VMs) and Application Service Adapter.
Consider these differences when moving applications between the two platforms.

## <a id="overview"></a>Buildpack overview

Application platforms such as TAS for VMs and Tanzu Application Platform use buildpacks to transform application source code and other assets into a self-contained artifact, such as a Cloud Foundry droplet or an Open Container Initiative container image. The platform then deploys this artifact to run instances of the application.

TAS for VMs uses Cloud Foundry buildpacks and provides a collection of system buildpacks that process many common languages and frameworks for cloud-native web applications. For more information about Cloud Foundry buildpacks, see [How Buildpacks Work](https://docs.pivotal.io/application-service/buildpacks/understand-buildpacks.html) in the TAS for VMs documentation.

Tanzu Application Platform instead uses Tanzu Buildpacks, which implement the newer Cloud Native Buildpack specification, to process application source code. For more information about Cloud Native Buildpacks, see the [Cloud Native Buildpacks project website](https://buildpacks.io/).

An installation of Tanzu Application Platform provides a default collection of Tanzu Buildpacks, and Application Service Adapter uses this same set of Tanzu Buildpacks to stage Cloud Foundry applications. Although this collection of Tanzu Buildpacks processes many of the same application languages and frameworks that the TAS for VMs system buildpacks do, there are some differences in the set of languages it can process, as well as differences in how certain buildpacks detect application code or accept configuration parameters.


## <a id="comparison"></a>System buildpack comparison

TAS for VMs provides the following system buildpacks for Linux-based applications:

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

For many applications using these buildpacks on TAS for VMs, the suite of Tanzu Buildpacks contains a cloud-native buildpack that can process its language, frameworks, and build configuration. Tanzu Buildpacks are not currently available for R, Ruby, and Staticfile applications, although community-based solutions might be available.

### <a id="binary"></a> Binary buildpack

Applications that use the [binary buildpack](https://docs.pivotal.io/application-service/buildpacks/binary/index.html) in TAS for VMs should use the [Tanzu Procfile Buildpack](https://docs.pivotal.io/tanzu-buildpacks/procfile/procfile-buildpack.html) with Application Service Adapter. Applications must include a Procfile that defines the process types and their start commands if they do not already.

### <a id="go"></a> Go buildpack

Applications that use the [Go buildpack](https://docs.pivotal.io/application-service/buildpacks/go/index.html) in TAS for VMs should use the [Tanzu Go Buildpack](https://docs.pivotal.io/tanzu-buildpacks/go/go-buildpack.html) with Application Service Adapter.

### <a id="java"></a> Java buildpack

Applications that use the [Java buildpack](https://docs.pivotal.io/application-service/buildpacks/java/index.html) in TAS for VMs should use the [Tanzu Java Buildpack](https://docs.pivotal.io/tanzu-buildpacks/java/java-buildpack.html) or the [Tanzu Java Native Image Buildpack](https://docs.pivotal.io/tanzu-buildpacks/java-native-image/java-native-image-buildpack.html) with Application Service Adapter.

### <a id="dotnet-core"></a> .NET Core buildpack

Applications that use the [.NET Core buildpack](https://docs.pivotal.io/application-service/buildpacks/dotnet-core/index.html) in TAS for VMs should use the [Tanzu .NET Core Buildpack](https://docs.pivotal.io/tanzu-buildpacks/dotnet-core/dotnet-core-buildpack.html) with Application Service Adapter. For more information about migrating an application to use this buildpack, see [Migrating to the Tanzu .NET Core Buildpack](https://docs.pivotal.io/tanzu-buildpacks/dotnet-core/dotnet-core-migration.html) in the Tanzu Buildpacks documentation.

### <a id="nginx"></a> NGINX buildpack

Applications that use the [NGINX buildpack](https://docs.pivotal.io/application-service/buildpacks/nginx/index.html) in TAS for VMs should use the [Tanzu NGINX Buildpack](https://docs.pivotal.io/tanzu-buildpacks/nginx/nginx-buildpack.html) with Application Service Adapter.

### <a id="nodejs"></a> Node.js buildpack

Applications that use the [Node.js buildpack](https://docs.pivotal.io/application-service/buildpacks/nodejs/index.html) in TAS for VMs should use the [Tanzu Node.js Buildpack](https://docs.pivotal.io/tanzu-buildpacks/nodejs/nodejs-buildpack.html) with Application Service Adapter. For more information about migrating an application to use this buildpack, see [Migrating to the Tanzu Node.js Buildpack](https://docs.pivotal.io/tanzu-buildpacks/nodejs/nodejs-migration.html) in the Tanzu Buildpacks documentation.

### <a id="php"></a> PHP buildpack

Applications that use the [PHP buildpack](https://docs.pivotal.io/application-service/buildpacks/php/index.html) in TAS for VMs should use the [Tanzu PHP Buildpack](https://docs.pivotal.io/tanzu-buildpacks/php/php-buildpack.html) with Application Service Adapter. For more information about migrating an application to use this buildpack, see [Migrating to the Tanzu PHP Buildpack](https://docs.pivotal.io/tanzu-buildpacks/php/php-migration.html) in the Tanzu Buildpacks documentation.

### <a id="python"></a> Python buildpack

Applications that use the [Python buildpack](https://docs.pivotal.io/application-service/buildpacks/python/index.html) in TAS for VMs should use the [Tanzu Python Buildpack](https://docs.pivotal.io/tanzu-buildpacks/python/python-buildpack.html) with Application Service Adapter.
