# Application Service Adapter for VMware Tanzu Application Platform


<p class="note warning">
<strong>Warning:</strong> Tanzu Application Platform is currently in
    beta and is intended only for evaluation and test purposes.
</p>

## Overview 

Application Service Adapter aims to provide Cloud Foundry (CF)  interface compatibility on top of VMware Tanzu Application Platform (TAP), so that existing CF and Tanzu Application Service (TAS) customers can transition to the Kubernetes based TAP without losing the familiar developer workflows on TAS and CF. This allows organizations to realize the operational and infrastructure benefits of TAP while deferring and reducing organization-wide costs of transitioning from TAS to TAP.

Platform operators will configure and install Application Service Adapter as a [Carvel package](https://carvel.dev/), as they would TAP and other Tanzu products deployed to Kubernetes. Application developers will log into Application Service Adapter with credentials for the underlying Kubernetes cluster, but will then use existing CF tools such as the CF CLI and the CF API to push apps, map routes, and retrieve logs. Kubernetes namespaces will back CF Spaces and Orgs, and TAP components such as the [Tanzu Build Service (TBS)](https://tanzu.vmware.com/build-service) and [Contour](https://projectcontour.io/) will build app images and realize ingress routes to running apps.

Here is a high level architecture with user flows:

![Application Service Adapter architecture diagram with user flows](images/architecture.png)


For installation and the supported versions of dependencies, see [Installing Prerequisite](install-prerequisites.md).

