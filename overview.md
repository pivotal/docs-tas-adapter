# Application Service Adapter for VMware Tanzu Application Platform


<p class="note warning">
<strong>Warning:</strong> The Application Service Adapter for VMware Tanzu Application Platform is currently in beta and is intended to be used only for evaluation and testing.
</p>

## Overview

Application Service Adapter aims to provide compatibility with [Cloud Foundry](https://www.cloudfoundry.org/) client interfaces to existing users of [Tanzu Application Service for VMs](https://tanzu.vmware.com/application-service) (TAS for VMs) while running on top of Kubernetes and integrating with the components of [Tanzu Application Platform](https://tanzu.vmware.com/application-platform). Development teams using TAS for VMs tooling, such as the Cloud Foundry command-line interface (cf CLI) and other clients of the Cloud Foundry APIs, can then maintain their familiar workflows while their platform teams transition their infrastructure and deployments to Kubernetes.

The following diagram shows a high level architecture of Application Service Adapter with user flows:

![Application Service Adapter architecture diagram with user flows](images/architecture.png)

Application Service Adapter is distributed as a [Carvel package](https://carvel.dev/) for platform teams to configure and install to a Kubernetes cluster with the Tanzu CLI tool. Application developers log into their Application Service Adapter installation with credentials for the underlying Kubernetes cluster, but then use the cf CLI and the Cloud Foundry API to push apps and to map routes to them.

Platform teams create Cloud Foundry orgs and spaces in the installation, which the Application Service Adapter backs with separate Kubernetes namespaces. Application developers then use these orgs and spaces to organize their apps as they do today with TAS for VMs.

Application Service Adapter integrates with an existing installation of [Tanzu Build Service](https://tanzu.vmware.com/build-service) component of Tanzu Application Platform to build container images for app code deployed to the platform. It also integrates with [Contour](https://projectcontour.io/) to realize ingress routes to running apps.

To install Application Service Adapter and its dependencies, proceed to [Installing Prerequisites](install-prerequisites.md).
