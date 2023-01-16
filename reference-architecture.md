# Reference Architecture

This topic gives you a path to creating a production deployment of
Application Service Adapter. However, do not feel constrained to follow this exact
path if your specific use cases warrant a different architecture.

Design decisions enumerated in this document exemplify the main design issues
you will encounter in planning your Application Service Adapter environment and
the rationale behind a chosen solution path. Understanding these decisions can
help provide a rationale for any necessary deviation from this architecture.

## <a id="tap-install"></a>Tanzu Application Platform installation

Application Service Adapter is installed on top of the Tanzu Application
Platform (TAP), and uses TAP components to build and deploy workloads. Because
both "build" and "run" steps are executed on the same cluster, it is necessary to
install the required TAP packages to a single cluster before installing the
Application Service Adapter. This type of installation maps most closely to the
"iterate cluster" described in the [TAP reference architecture](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.2/tap-reference-architecture/GUID-reference-designs-tap-architecture-planning.html#iterate-cluster-requirements-10).

Some packages included in an "iterate cluster" installation are optional when
preparing a cluster for Application Service Adapter. Required packages are as
follows:

```
buildservice.tanzu.vmware.com
cert-manager.tanzu.vmware.com
contour.tanzu.vmware.com
service-bindings.labs.vmware.com
tap.tanzu.vmware.com
tap-telemetry.tanzu.vmware.com
```

If you plan to enable the experimental Cartographer integration in the
Application Service Adapter, the following packages are also required:

```
tekton.tanzu.vmware.com
cartographer.tanzu.vmware.com
ootb-templates.tanzu.vmware.com
controller.source.apps.tanzu.vmware.com
controller.conventions.apps.tanzu.vmware.com
```

## <a id="adapter-reqs"></a>Adapter system requirements

Application Service Adapter installs a Cloud Foundry API server and a set of
controller runtime components on top of the base TAP installation. Although
these components will consume cluster resources, the recommended minimum sizing
for a TAP "iterate cluster" should be sufficient to accomodate Application
Service Adapter system components running with modest load.

Specifically, TAP recommends the following for an "iterate cluster":

- LoadBalancer for ingress controller (2 external IP addresses)
- Default storage class
- At least 16 GB available memory that is allocatable across clusters, with at least 8 GB per node
- Logging is enabled and targets the desired application logging platform
- Monitoring is enabled and targets the desired application observability platform
- Spread across three AZs for high availability

Note that with increased API traffic or deployed object counts, Application
Service Adapter components may need to be scaled as described in the [scaling
topic](scaling.md) which may require additional cluster resources.

## <a id="app-reqs"></a>Application workload requirements

Since `cf push`ed applications are run as Pods on the same cluster as the
Application Service Adapter itself, you should plan the capacity of your cluster
to accomodate those applications.

Planning capacity for application workloads depends heavily on the applications
you plan to deploy and how they are scaled, but default memory, cpu, and disk
allocations for each application instance are as follows:

```
limits:
  ephemeral-storage: 1Gi
  memory: 1Gi
requests:
  cpu: 100m
  ephemeral-storage: 1Gi
  memory: 1Gi
```

Planning total capacity on your cluster is largely a matter of understanding the
size of your planned applications, and then adding those resources up to make
sure you have sufficient resources on your cluster.
