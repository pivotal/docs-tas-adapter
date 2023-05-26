# Application Service Adapter reference architecture

The Application Service Adapter architecture described here gives you a
path to create a production deployment of Application Service Adapter.

Your use case may warrant a different architecture, but the design decisions
described here exemplify the main design issues in planning your Application
Service Adapter environment. Understanding these decisions can help provide a
rationale for any necessary deviation from this architecture.

## <a id="tap-install"></a>Tanzu Application Platform installation

Application Service Adapter is installed on top of the Tanzu Application
Platform, and uses Tanzu Application Platform components to build and deploy workloads. Because
both "build" and "run" steps are executed on the same cluster, you must
install the required Tanzu Application Platform packages to a single cluster before installing the
Application Service Adapter. This type of installation maps most closely to the
"iterate cluster" described in the [Tanzu Application Platform reference architecture](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.2/tap-reference-architecture/GUID-reference-designs-tap-architecture-planning.html#iterate-cluster-requirements-10).

Some packages included in an "iterate cluster" installation are optional when
preparing a cluster for Application Service Adapter. The required packages are:

```bash
buildservice.tanzu.vmware.com
cert-manager.tanzu.vmware.com
contour.tanzu.vmware.com
service-bindings.labs.vmware.com
tap.tanzu.vmware.com
tap-telemetry.tanzu.vmware.com
```

If you plan to enable the experimental Cartographer integration in the
Application Service Adapter, the following packages are also required:

```bash
tekton.tanzu.vmware.com
cartographer.tanzu.vmware.com
ootb-templates.tanzu.vmware.com
controller.source.apps.tanzu.vmware.com
controller.conventions.apps.tanzu.vmware.com
```

## <a id="adapter-reqs"></a>Adapter system requirements

Application Service Adapter installs a Cloud Foundry API server and a set of
controller runtime components on top of the base Tanzu Application Platform installation. Although
these components consume cluster resources, the recommended minimum sizing
for a Tanzu Application Platform "iterate cluster" are sufficient to accommodate Application
Service Adapter system components running with modest load.

Specifically, VMware recommends the following for an "iterate cluster":

- LoadBalancer for ingress controller (2 external IP addresses).
- Default storage class.
- At least 16&nbsp;GB available memory that is allocatable across clusters, with at least 8&nbsp;GB per node.
- Logging is enabled and targets the desired application logging platform.
- Monitoring is enabled and targets the desired application observability platform.
- Spread across three AZs for high availability.

> **Note** With increased API traffic or deployed object counts, Application
Service Adapter components might need to be scaled as described in the [scaling
topic](scaling.md) and might require additional cluster resources.

## <a id="app-reqs"></a>Application workload requirements

Because `cf push`ed applications run as pods on the same cluster as the
Application Service Adapter itself, plan the capacity of your cluster
to accommodate those applications.

Planning capacity for application workloads depends on the applications
you plan to deploy and how they are scaled. Default memory, CPU, and disk
allocations for each application instance are as follows:

```bash
limits:
  ephemeral-storage: 1Gi
  memory: 1Gi
requests:
  cpu: 100m
  ephemeral-storage: 1Gi
  memory: 1Gi
```

Planning total capacity on your cluster requires understanding the
size of your planned applications, and then adding up those resources to ensure
that you have sufficient resources on your cluster.
