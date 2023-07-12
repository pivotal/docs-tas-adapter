# Cleaning up old resources with Application Service Adapter

When you push an app with Application Service Adapter, resources can accumulate.
Read this topic for guidance in cleaning up old resources.

- Every source code upload results in a CFPackage resource on the cluster and a
  corresponding container image holding the source in the sources repository.
- Every build of the sources results in a CFBuild resource and a corresponding
container image holding the built image in the droplets repository.

To minimize container image registry storage costs, Application Service Adapter
deletes older CFPackage and CFBuild resources and attempts to remove the
corresponding container images. Image deletion failures are logged, but do
not block resource deletion.

## <a id="cf-packages"></a>CFPackage resources

Application Service Adapter uses the config property
`cleanup.max_retained_packages_per_app` to determine when to remove old
CFPackage resources. The default value is 5, but you can set it to any positive
integer. The CFPackage resource corresponding to the current droplet of the app
is retained. The next _N_ CFPackage resources with sources
uploaded, by creation date in descending order, are also retained, where _N_ is
the value from `cleanup.max_retained_packages_per_app`. Other CFPackage
resources with sources uploaded are deleted, which also triggers
deletion of their container registry images.

## <a id="cf-builds"></a>CFBuild resources

Application Service Adapter uses the config property
`cleanup.max_retained_builds_per_app` to determine when to remove old CFBuild
resources. The default value is 5, but you can set it to any positive integer.
The CFBuild resource corresponding to the current droplet of the app is always
retained. The next _N_ CFBuild resources with sources built, by
creation date in descending order, are also retained, where _N_ is the value
from `cleanup.max_retained_builds_per_app`. Other CFBuild resources with sources built are deleted, which also triggers deletion of their container
registry images.

## <a id="repositories"></a>Repositories

Two container registry repositories are created for each app pushed by
Application Service Adapter: one for sources, one for droplets. Application Service Adapter does not delete these repositories when an app is
deleted. App deletion deletes all related CFPackage and CFBuild resources, so
the container registry images are deleted and the sources and droplets
repositories are empty.

Some registries, such as Azure Container Registry and Google Container Registry,
do not show repositories without images, so no manual repository cleanup is
required. Other registries show the empty repositories. These can be safely
deleted by using the UI or API of the particular registry.
