# CF Buildpack to Cloud Native Buildpacks Migration
Cloud Native Buildpacks(CNB) are a newer version of the Buildpack specification used in TAS for VMs. Outside of a few filesystem/interface differences the user experience is largely the same.  
This document will list the buildpacks present in TAS for VMs and link to their corresponding CNB documentation pages.

To learn more about the buildpack spec, look [here](https://buildpacks.io/).

Aside from language-specific buildpack differences, buildpack configuration now occurs through environment variables as opposed to the `buildpack.yml` file. See the https://github.com/paketo-buildpacks/apache-tomcat configuration section for an example of that interface.

# TAS for VMs Buildpacks
```
$ cf buildpacks
Getting buildpacks as admin...

position   name                     stack         
1          staticfile_buildpack     cflinuxfs3    
2          java_buildpack_offline   cflinuxfs3    
3          ruby_buildpack           cflinuxfs3    
4          nginx_buildpack          cflinuxfs3    
5          nodejs_buildpack         cflinuxfs3    
6          go_buildpack             cflinuxfs3    
7          r_buildpack              cflinuxfs3    
8          python_buildpack         cflinuxfs3    
9          php_buildpack            cflinuxfs3    
10         dotnet_core_buildpack    cflinuxfs3    
11         binary_buildpack         cflinuxfs3    
12         binary_buildpack         windows2016   
13         binary_buildpack         windows       
```

* **NOTE** Cf-for-vms binary buildpack has been renamed to procfile buildpack


# Tanzu Build Service Buildpacks
Below are the buildpacks included in a light-profile TAP installation.
```
apiVersion: kpack.io/v1alpha2
kind: ClusterBuilder
metadata:
  name: default
spec:
  order:
  - group:
    - id: tanzu-buildpacks/dotnet-core-lite
  - group:
    - id: tanzu-buildpacks/nodejs-lite
  - group:
    - id: tanzu-buildpacks/go-lite
  - group:
    - id: tanzu-buildpacks/python-lite
  - group:
    - id: tanzu-buildpacks/java-native-image-lite
  - group:
    - id: tanzu-buildpacks/java-lite
  serviceAccountRef:
    name: kp-default-repository-serviceaccount
    namespace: kpack
  stack:
    kind: ClusterStack
    name: base
  store:
    kind: ClusterStore
    name: default
  tag: gcr.io/tas4k8s-engineering/em/tas/2021-12-22:clusterbuilder-default
```


# Equivalent Cloud Native Buildpacks Documentation Pages
| Paketo Buildpack | Documentation                                                                   |
| ---------------- | ------------------------------------------------------------------------------- |
| staticfile       | (community) https://github.com/paketo-community/staticfile                      |
| java             | https://docs.pivotal.io/tanzu-buildpacks/java/java-buildpack.html               |
| ruby             | (open source) https://paketo.io/docs/howto/ruby/                                |
| nginx            | https://docs.pivotal.io/tanzu-buildpacks/nginx/nginx-buildpack.html             |
| nodejs           | https://docs.pivotal.io/tanzu-buildpacks/nodejs/nodejs-buildpack.html           |
| go               | https://docs.pivotal.io/tanzu-buildpacks/go/go-buildpack.html                   |
| r                |                                                                                 |
| python           | https://docs.pivotal.io/tanzu-buildpacks/python/python-buildpack.html           |
| dotnet           | https://docs.pivotal.io/tanzu-buildpacks/dotnet-core/dotnet-core-buildpack.html |
| php              | https://docs.pivotal.io/tanzu-buildpacks/php/php-buildpack.html                 |
| binary           | (open source) https://github.com/paketo-buildpacks/procfile                     |


## Specific Buildpack Migration Instructions
The following buildpacks are identified as requiring specific migration instructions when upgrading to Paketo/TBS.
### [.NET Buildpack Migration ](https://docs.pivotal.io/tanzu-buildpacks/dotnet-core/dotnet-core-migration.html)

### [Node.js Buildpack Migration](https://docs.pivotal.io/tanzu-buildpacks/nodejs/nodejs-migration.html)

### [PHP Buildpack Migration](https://docs.pivotal.io/tanzu-buildpacks/php/php-migration.html)
