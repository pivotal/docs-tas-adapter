# CF Buildpack to Buildpacks v3 Migration
CloudNative Buildpacks(CNB) are a newer version of the Buildpack specification used in CF for VMs. Outside of a few filesystem/interface differences the user experience is largely the same.  
This document will list the buildpacks present in CF for VMs and link to their corresponding CNB documentation pages.

To learn more about the buildpack spec, look [here](https://buildpacks.io/).

Aside from language-specific buildpack differences, buildpack configuration now occurs through environment variables as opposed to the `buildpack.yml` file. See the https://github.com/paketo-buildpacks/apache-tomcat configuration section for an example of that interface.

# CF for VMs buildpacks
```
$ cf buildpacks
Getting buildpacks as admin...

position name stack enabled locked filename

1 staticfile_buildpack cflinuxfs3 true false staticfile_buildpack-cflinuxfs3-v1.5.27.zip

2 java_buildpack cflinuxfs3 true false java-buildpack-cflinuxfs3-v4.45.zip

3 ruby_buildpack cflinuxfs3 true false ruby_buildpack-cflinuxfs3-v1.8.49.zip

4 dotnet_core_buildpack cflinuxfs3 true false dotnet-core_buildpack-cflinuxfs3-v2.3.37.zip

5 nodejs_buildpack cflinuxfs3 true false nodejs_buildpack-cflinuxfs3-v1.7.65.zip

6 go_buildpack cflinuxfs3 true false go_buildpack-cflinuxfs3-v1.9.38.zip

7 python_buildpack cflinuxfs3 true false python_buildpack-cflinuxfs3-v1.7.48.zip

8 php_buildpack cflinuxfs3 true false php_buildpack-cflinuxfs3-v4.4.53.zip

9 nginx_buildpack cflinuxfs3 true false nginx_buildpack-cflinuxfs3-v1.1.33.zip

10 r_buildpack cflinuxfs3 true false r_buildpack-cflinuxfs3-v1.1.25.zip

11 binary_buildpack cflinuxfs3 true false binary_buildpack-cflinuxfs3-v1.0.42.zip
```

* **NOTE** Cf-for-vms binary buildpack has been renamed to procfile buildpack

# TAS for VMs Buildpack
| Paketo Buildpack  | Documentation   |
|--|--|
| staticfile | (community) https://github.com/paketo-community/staticfile   |
| java | https://docs.pivotal.io/tanzu-buildpacks/java/java-buildpack.html    |
| ruby |  (open source) https://paketo.io/docs/howto/ruby/   |
| dotnet | https://docs.pivotal.io/tanzu-buildpacks/dotnet-core/dotnet-core-buildpack.html    |
| nodejs | https://docs.pivotal.io/tanzu-buildpacks/nodejs/nodejs-buildpack.html     |
| go | https://docs.pivotal.io/tanzu-buildpacks/go/go-buildpack.html     |
| python | https://docs.pivotal.io/tanzu-buildpacks/python/python-buildpack.html   |
| php | https://docs.pivotal.io/tanzu-buildpacks/php/php-buildpack.html    |
| nginx | https://docs.pivotal.io/tanzu-buildpacks/nginx/nginx-buildpack.html    |
| r |    |
| binary |   https://github.com/paketo-buildpacks/procfile     |



## Specific Buildpack Migration Instructions
The following buildpacks are identified as requiring specific migration instructions when upgrading to Packeto/TBS.
### [.NET Buildpack Migration ](https://docs.pivotal.io/tanzu-buildpacks/dotnet-core/dotnet-core-migration.html)

### [Node.js Buildpack Migration](https://docs.pivotal.io/tanzu-buildpacks/nodejs/nodejs-migration.html)

### [PHP Buildpack Migration](https://docs.pivotal.io/tanzu-buildpacks/php/php-migration.html)

### Java Buildpack Migration *coming soon*

# TAS-Adapter Buildpacks
```
apiVersion: kpack.io/v1alpha2  
kind: ClusterStore  
metadata:  
name: cf-default-buildpacks  
spec:  
sources:  
- image: gcr.io/paketo-buildpacks/java  
- image: gcr.io/paketo-buildpacks/nodejs  
- image: gcr.io/paketo-buildpacks/ruby  
- image: gcr.io/paketo-buildpacks/procfile  
- image: gcr.io/paketo-buildpacks/go  
```

| CF on K8s Buildpack  | Buildpack Documentation  |
| -- | -- |
|java |https://docs.pivotal.io/tanzu-buildpacks/java/java-buildpack.html  |
|nodejs  | https://docs.pivotal.io/tanzu-buildpacks/nodejs/nodejs-buildpack.html  |
|ruby| (open source) https://paketo.io/docs/howto/ruby/  |
|go | https://docs.pivotal.io/tanzu-buildpacks/go/go-buildpack.html  |
|procfile |https://github.com/paketo-buildpacks/procfile|