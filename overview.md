# Application Service Adapter for VMware Tanzu Application Platform

## Overview

Application Service Adapter aims to provide compatibility with [Cloud Foundry](https://www.cloudfoundry.org/) client interfaces to existing users of [Tanzu Application Service for VMs](https://tanzu.vmware.com/application-service) while running on top of Kubernetes and integrating with the components of [Tanzu Application Platform](https://tanzu.vmware.com/application-platform). Development teams using Tanzu Application Service for VMs tooling, such as the Cloud Foundry command-line interface (cf CLI) and other clients of the Cloud Foundry API (CAPI), can then maintain their familiar workflows while their platform teams transition their infrastructure and deployments to Kubernetes.

The following diagram shows a high-level architecture of Application Service
Adapter with user flows. These are also described in the following sections.

![alt-text="Application Service Adapter architecture diagram shows user flows."](images/architecture.png)

Application Service Adapter is distributed as a [Carvel package](https://carvel.dev/) for platform teams to configure and install to a Kubernetes cluster with the Tanzu CLI tool. The key user personas of Application Service Adapter remain the same as the user personas of Tanzu Application Serivce: the platform operator and the app developer.

Platform teams create Cloud Foundry orgs and spaces in the installation, which the Application Service Adapter backs with separate Kubernetes namespaces. Application developers then use these orgs and spaces to organize their apps as they do today with Tanzu Application Service for VMs.

Application developers log in to their Application Service Adapter installation with credentials for the underlying Kubernetes cluster, but then use the cf CLI and CAPI to push apps and to map routes to them.

Application Service Adapter integrates with an existing installation of [Tanzu Build Service](https://tanzu.vmware.com/build-service) component of Tanzu Application Platform to build container images for app code deployed to the platform. It also integrates with [Contour](https://projectcontour.io/) to realize ingress routes to running apps.

To learn about Kubernetes cluster requirements and to plan your installation, see [Reference architecture](reference-architecture.md).

To install Application Service Adapter and its dependencies, go to [Installing prerequisites](install-prerequisites.md).

## <a id='telemetry-notice'></a> Notice of telemetry collection for Application Service Adapter

[//]: # (This following text came from legal. Do not edit it.)

Application Service Adapter for VMware Tanzu Application Platform participates in the VMware Customer Experience Improvement Program (CEIP).
As part of CEIP, VMware collects technical information about your organization’s use of VMware
products and services in association with your organization’s VMware license keys.
For information about CEIP, see the [Trust & Assurance Center](https://www.vmware.com/solutions/trustvmware/ceip.html).
You may join or leave CEIP at any time.
The CEIP Standard Participation Level provides VMware with information to improve its products and
services, identify and fix problems, and advise you on how to best deploy and use VMware products.
For example, this information can enable a proactive product deployment discussion with your VMware
account team or VMware support team to help resolve your issues.
This information cannot directly identify any individual.

[//]: # (The text above came from legal. Do not edit it.)

You must acknowledge that you have read the VMware CEIP policy before you can proceed with the
installation.
For more information, see [Tanzu Application Platform Telemetry](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.5/tap/telemetry-overview.html).
To opt out of CEIP participation after installation, see
[Opt out of telemetry collection](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.5/tap/opting-out-telemetry.html).
