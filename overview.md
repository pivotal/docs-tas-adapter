# Application Service Adapter for VMware Tanzu Application Platform


<p class="note warning">
<strong>Warning:</strong> The Application Service Adapter for VMware Tanzu Application Platform is currently in beta and is intended to be used only for evaluation and testing.
</p>

## Overview 

The Application Service Adapter aims to provide compatibility with [Cloud Foundry](https://www.cloudfoundry.org/) (CF) client interfaces to existing users of the [Tanzu Application Service](https://tanzu.vmware.com/application-service) (TAS), while running on top of Kubernetes and integrating with the components of the [VMware Tanzu Application Platform](https://tanzu.vmware.com/application-platform) (TAP). Development teams using TAS tooling such as the CF CLI and other clients of the CF APIs can then maintain their familiar workflows while their platform teams transition their infrastructure and deployments to Kubernetes.

The Application Service Adapter is distributed as a [Carvel package](https://carvel.dev/) for platform teams to configure and install to a Kubernetes cluster with the Carvel toolchain or with the Tanzu CLI tool. Application developers log into their Application Service Adapter installation with credentials for the underlying Kubernetes cluster, but then use the CF CLI and the CF API to push apps and to map routes to them.

Platform teams create CF Orgs and Spaces in the installation, which the Application Service Adapter backs with separate Kubernetes namespaces. Application developers then use these orgs and spaces to organize their apps as they do today with TAS.

The Application Service Adapter integrates with an existing installation of [Tanzu Build Service](https://tanzu.vmware.com/build-service) (TBS) component of TAP to build container images for app code deployed to the platform. It also integrates with [Contour](https://projectcontour.io/) to realize ingress routes to running apps.

Here is a high level architecture with user flows:

![Application Service Adapter architecture diagram with user flows](images/architecture.png)


To install the Application Service Adapter and its dependencies, proceed to [Installing Prerequisites](install-prerequisites.md).
