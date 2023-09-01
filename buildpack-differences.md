# Buildpacks used by Application Service Adapter

Application Service Adapter uses different buildpacks than TAS for VMs, which you need to know
when moving applications between the two platforms. This topic describes the
differences between the two buildpack systems.

## <a id="overview"></a>Differences between the buildpack systems

Application platforms such as Tanzu Application Service for VMs and Tanzu Application Platform use buildpacks to transform application source code and other assets into a self-contained artifact, such as a Cloud Foundry droplet or an Open Container Initiative container image. The platform then deploys this artifact to run instances of the application.

Tanzu Application Service for VMs uses Cloud Foundry buildpacks and provides a collection of system buildpacks that process many common languages and frameworks for cloud-native web applications. For more information about Cloud Foundry buildpacks, see [How Buildpacks Work](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/3.0/tas-for-vms/understand-buildpacks.html) in the Tanzu Application Service for VMs documentation.

Tanzu Application Platform instead uses Tanzu Buildpacks, which implement the later Cloud Native Buildpack specification to process application source code. For more information about Cloud Native Buildpacks, see the [Cloud Native Buildpacks project website](https://buildpacks.io/).

An installation of Tanzu Application Platform provides a default collection of Tanzu Buildpacks, and Application Service Adapter uses this same set of Tanzu Buildpacks to stage Cloud Foundry applications. Although this collection of Tanzu Buildpacks processes many of the same application languages and frameworks that the Tanzu Application Service for VMs system buildpacks do, there are some differences in the set of languages it can process, as well as differences in how certain buildpacks detect application code or accept configuration parameters.


## <a id="comparison"></a>System buildpack comparison

Tanzu Application Service for VMs provides the following system buildpacks for Linux-based applications:

- [Binary buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/binary-index.html)
- [Go buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/go-index.html)
- [Java buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/java-index.html)
- [.NET Core buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/dotnet-core-index.html)
- [NGINX buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/nginx-index.html)
- [Node.js buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/node-index.html)
- [PHP buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/php-index.html)
- [Python buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/python-index.html)
- [R buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/r-index.html)
- [Ruby buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/ruby-index.html)
- [Staticfile buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/staticfile-index.html)

For many applications using these buildpacks on Tanzu Application Service for VMs, the suite of Tanzu Buildpacks contains a cloud-native buildpack that can process its language, frameworks, and build configuration. Tanzu Buildpacks are not currently available for R applications, although community-based solutions might be available.

### <a id="binary"></a> Binary buildpack

Applications that use the [binary buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/binary-index.html) in Tanzu Application Service for VMs should use the [Tanzu Procfile Buildpack](https://docs.vmware.com/en/VMware-Tanzu-Buildpacks/services/tanzu-buildpacks/GUID-procfile-procfile-buildpack.html) with Application Service Adapter. Applications must include a Procfile that defines the process types and their start commands if they do not already.

### <a id="go"></a> Go buildpack

Applications that use the [Go buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/go-index.html) in Tanzu Application Service for VMs should use the [Tanzu Go Buildpack](https://docs.vmware.com/en/VMware-Tanzu-Buildpacks/services/tanzu-buildpacks/GUID-go-go-buildpack.html) with Application Service Adapter.

### <a id="java"></a> Java buildpack

Applications that use the [Java buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/java-index.html) in Tanzu Application Service for VMs should use the [Tanzu Java Buildpack](https://docs.vmware.com/en/VMware-Tanzu-Buildpacks/services/tanzu-buildpacks/GUID-java-java-buildpack.html) or the [Tanzu Java Native Image Buildpack](https://docs.vmware.com/en/VMware-Tanzu-Buildpacks/services/tanzu-buildpacks/GUID-java-native-image-java-native-image-buildpack.html) with Application Service Adapter.

### <a id="dotnet-core"></a> .NET Core buildpack

Applications that use the [.NET Core buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/dotnet-core-index.html) in Tanzu Application Service for VMs should use the [Tanzu .NET Core Buildpack](https://docs.vmware.com/en/VMware-Tanzu-Buildpacks/services/tanzu-buildpacks/GUID-dotnet-core-dotnet-core-buildpack.html) with Application Service Adapter. For more information about migrating an application to use this buildpack, see [Migrating to the Tanzu .NET Core Buildpack](https://docs.vmware.com/en/VMware-Tanzu-Buildpacks/services/tanzu-buildpacks/GUID-nodejs-nodejs-migration.html) in the Tanzu Buildpacks documentation.

### <a id="nginx"></a> NGINX buildpack

Applications that use the [NGINX buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/nginx-index.html) in Tanzu Application Service for VMs should use the [Tanzu NGINX Buildpack](https://docs.vmware.com/en/VMware-Tanzu-Buildpacks/services/tanzu-buildpacks/GUID-nginx-nginx-buildpack.html) with Application Service Adapter.

### <a id="nodejs"></a> Node.js buildpack

Applications that use the [Node.js buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/node-index.html) in Tanzu Application Service for VMs should use the [Tanzu Node.js Buildpack](https://docs.vmware.com/en/VMware-Tanzu-Buildpacks/services/tanzu-buildpacks/GUID-nodejs-nodejs-buildpack.html) with Application Service Adapter. For more information about migrating an application to use this buildpack, see [Migrating to the Tanzu Node.js Buildpack](https://docs.vmware.com/en/VMware-Tanzu-Buildpacks/services/tanzu-buildpacks/GUID-nodejs-nodejs-migration.html) in the Tanzu Buildpacks documentation.

### <a id="php"></a> PHP buildpack

Applications that use the [PHP buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/php-index.html) in Tanzu Application Service for VMs should use the [Tanzu PHP Buildpack](https://docs.vmware.com/en/VMware-Tanzu-Buildpacks/services/tanzu-buildpacks/GUID-php-php-buildpack.html) with Application Service Adapter. For more information about migrating an application to use this buildpack, see [Migrating to the Tanzu PHP Buildpack](https://docs.vmware.com/en/VMware-Tanzu-Buildpacks/services/tanzu-buildpacks/GUID-php-php-migration.html) in the Tanzu Buildpacks documentation.

### <a id="python"></a> Python buildpack

Applications that use the [Python buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/python-index.html) in Tanzu Application Service for VMs should use the [Tanzu Python Buildpack](https://docs.vmware.com/en/VMware-Tanzu-Buildpacks/services/tanzu-buildpacks/GUID-python-python-buildpack.html) with Application Service Adapter.

### <a id="ruby"></a> Ruby buildpack

Applications that use the [Ruby buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/ruby-index.html) in Tanzu Application Service for VMs should use the [Tanzu Ruby Buildpack](https://docs.vmware.com/en/VMware-Tanzu-Buildpacks/services/tanzu-buildpacks/GUID-ruby-ruby-buildpack.html) with Application Service Adapter.

### <a id="staticfile"></a> Staticfile buildpack

Applications that use the [Staticfile buildpack](https://docs.vmware.com/en/VMware-Tanzu-Application-Service/4.0/tas-for-vms/staticfile-index.html) in Tanzu Application Service for VMs should use the [Tanzu Web Servers Buildpack](https://docs.vmware.com/en/VMware-Tanzu-Buildpacks/services/tanzu-buildpacks/GUID-web-servers-web-servers-buildpack.html) with Application Service Adapter.
