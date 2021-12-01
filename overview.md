# Application Service Adapter for VMware Tanzu Application Platform


<p class="note warning">
<strong>Warning:</strong> Tanzu Application Platform is currently in
    beta and is intended for evaluation and test purposes only.
</p>

## Overview

Application Service Adapter aims to provide Cloud Foundry (CF) interface compatibility on top of VMware Tanzu Application Platform (TAP) so that existing CF and Tanzu Application Service for VMs (TAS for VMs) customers can transition to the Kubernetes-based TAP without losing the familiar developer workflows on TAS for VMs and CF. This allows organizations to realize the operational and infrastructure benefits of TAP while deferring and reducing organization-wide costs of transitioning from TAS for VMs to TAP.

Platform operators install and configure Application Service Adapter as a [Carvel package](https://carvel.dev/) as they would install and configure TAP and other Tanzu products deployed to Kubernetes. Application developers log in to Application Service Adapter with credentials for the underlying Kubernetes cluster, and then use existing CF tools such as the Cloud Foundry command-line interface (cf CLI) and the Cloud Foundry API (CAPI) to push apps, map routes, and retrieve logs. Kubernetes namespaces back CF Spaces and Orgs, and TAP components such as the [Tanzu Build Service (TBS)](https://tanzu.vmware.com/build-service) and [Contour](https://projectcontour.io/) build app images and realize ingress routes to running apps.

The following diagram shows a high level architecture of Application Service Adapter with user flows:

![Application Service Adapter architecture diagram with user flows](images/architecture.png)

For installation and the supported versions of dependencies, see [Installing Prerequisite](install-prerequisites.md).
