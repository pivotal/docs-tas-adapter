# Cleaning up old resources

When an app is pushed using Application Service Adapter, a number of resources are created that can build up over time.
Each source code upload results in a CFPackage resource on the cluster, and a corresponding container image holding the source in the sources repository.
Each build of the sources results in a CFBuild resource, and a corresponding container image holding the built image in the droplets repository.
To minimize container registry storage costs, Application Service Adapter will delete older CFPackage and CFBuild resources, and will make a best-effort attempt to remove the corresponding container images.
Any image deletion failures will be logged, but will not block resource deletion.

## <a id="cf-packages"></a>CFPackage resources

Application Service Adapter uses the config property `cleanup.max_retained_packages_per_app` to determine when to remove old CFPackage resources.
The default value is 5, and it can be set to any positive integer.
The CFPackage resource corresponding to the current droplet of the app is always retained.
The next _N_ CFPackage resources with sources successfully uploaded, ordered by creation date descending, are also retained, where _N_ is the value from `cleanup.max_retained_packages_per_app`.
Other CFPackage resources with sources successfully uploaded are deleted, which also triggers deletion of their container registry images.

## <a id="cf-builds"></a>CFBuild resources

Application Service Adapter uses the config property `cleanup.max_retained_builds_per_app` to determine when to remove old CFBuild resources.
The default value is 5, and it can be set to any positive integer.
The CFBuild resource corresponding to the current droplet of the app is always retained.
The next _N_ CFBuild resources with sources successfully built, ordered by creation date descending, are also retained, where _N_ is the value from `cleanup.max_retained_builds_per_app`.
Other CFBuild resources with sources successfully build are deleted, which also triggers deletion of their container registry images.

## <a id="repositories"></a>Repositories

Two container registry repositories are created for each app pushed by Application Service Adapter: one for sources, one for droplets.
Currently Application Service Adapter does not delete these repositories when an app is deleted.
App deletion deletes all related CFPackage and CFBuild resources, so the container registry images will be deleted, and the sources and droplets repositories will be empty.

Some registries, such as Azure Container Registry and Google Container Registry, do not show repositories without images, so no manual repository cleanup is required.
Other registries will show the empty repositories.
These can be safely deleted using the UI or API of the particular registry.
