# Upgrade Application Service Adapter

This document describes how to upgrade Application Service Adapter for VMware Tanzu Application Platform.

You can perform a fresh installation of Application Service Adapter by following the instructions in [Install Application Service Adapter](install.md).

When upgrading to a new major or minor version of Application Service Adapter, see the documentation for that version for version-specific configuration and upgrade instructions.

You can find new patch versions of Application Service Adapter on the TanzuNet product page directly, or [sign up to receive email alerts](https://network.tanzu.vmware.com/docs/faq#alerts) when the product is updated.

> **Note** Upgrades are not currently supported if you have enabled the experimental Cartographer integration. Existing application workloads do not behave correctly after upgrading from v1.0 to v1.1.

## <a id='prereqs'></a> Prerequisites

Before you upgrade Application Service Adapter:

- Verify that you meet all the [prerequisites](install-prerequisites.md) of the target Tanzu Application Service Adapter version. If the target Tanzu Application version does not support your existing Kubernetes version, VMware recommends upgrading to a supported version before proceeding with the upgrade.
- Back up important cluster state, Kubernetes `etcd` and the container registries used for storing app images, as described in the [Disaster Recovery](disaster-recovery.md) topic.
- [Install or update the Tanzu CLI and plugins](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.2/tap/GUID-install-tanzu-cli.html#install-or-update-the-tanzu-cli-and-plugins-3)
  - For information about installing or updating the Tanzu CLI and plug-ins, see [Install or update the Tanzu CLI and plugins](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.2/tap/GUID-install-tanzu-cli.html#install-or-update-the-tanzu-cli-and-plugins-3).
- Verify that all packages are reconciled by running `tanzu package installed list -A`.
- It is strongly recommended to [upgrade](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.2/tap/GUID-upgrading.html) the Tanzu Application Platform version to the latest patch version of the currently installed major-minor (for example, 1.2).

## <a id="add-new-package-repo"></a> Update the new package repository

Follow these steps to update the new package repository:

1. Add the target version of the Application Service Adapter package repository:

    ```bash
    tanzu package repository add tas-adapter-repository \
      --url registry.tanzu.vmware.com/app-service-adapter/tas-adapter-package-repo:${TAS_ADAPTER_VERSION} \
      --namespace tap-install
    ```

1. Verify you have added the new package repository by running:

    ```bash
    tanzu package available list \
      --namespace tap-install
    ```

## <a id="upgrading-to-version-1-1"></a> Upgrading to v1.2.0

1. Update your `tas-adapter-values.yml` file.

   > **Note** VMware recommends making a backup copy of `tas-adapter-values.yml` before modifying it.

   Image repositories are now being created for each app instead of using one repo for all images. You are no longer required to specify paths for `packages` and `droplets`.

   Edit your `tas-adapter-values.yml` and remove the following config:

   ```yaml
   app_registry:
    hostname: "APP-REGISTRY-HOSTNAME"
    path:
      droplets: "APP-REGISTRY-PATH-DROPLETS"
      packages: "APP-REGISTRY-PATH-PACKAGES"
   ```

   Replace it with the following config:

   ```yaml
   app_registry:
      repository_prefix: "REPOSITORY-PREFIX"
   ```

   Where `REPOSITORY-PREFIX` is the host and path combination used as the base
   for package and droplet images produced by Application Service Adapter. For
   example: If `REPOSITORY-PREFIX` is `gcr.io/tas-adapter-`, then app's package
   images are stored at `gcr.io/tas-adapter-<app-guid>-packages` and app's
   droplet images are stored at `gcr.io/tas-adapter-<app-guid>-droplets`.

## <a id="perform-the-upgrade-of-application-service-adapter"></a> Perform the upgrade of Application Service Adapter

To upgrade, run:

```bash
tanzu package installed update tas-adapter \
  --package-name application-service-adapter.tanzu.vmware.com \
  --version "${TAS_ADAPTER_VERSION}" \
  --values-file tas-adapter-values.yml \
  --namespace tap-install
```

Where `TAS_ADAPTER_VERSION` is the target revision of Application Service Adapter you are migrating to.

## <a id="verify"></a> Verify the upgrade

> **Important** Run the following command in the directory where the `tas-adapter-values.yml` file resides.

To verify the versions of packages after the upgrade, run:

```bash
tanzu package installed list --namespace tap-install
```

Your output should be similar, but probably not identical, to the following example output:

```console
  Retrieving installed packages...
  NAME                        PACKAGE-NAME                                 PACKAGE-VERSION  STATUS
  api-auto-registration       apis.apps.tanzu.vmware.com                   0.1.1            Reconcile succeeded
  appsso                      sso.apps.tanzu.vmware.com                    2.0.0            Reconcile succeeded
  buildservice                buildservice.tanzu.vmware.com                1.7.2            Reconcile succeeded
  cartographer                cartographer.tanzu.vmware.com                0.5.3            Reconcile succeeded
  cert-manager                cert-manager.tanzu.vmware.com                1.7.2+tap.1      Reconcile succeeded
  contour                     contour.tanzu.vmware.com                     1.22.0+tap.4     Reconcile succeeded
  eventing                    eventing.tanzu.vmware.com                    2.0.1            Reconcile succeeded
  ootb-templates              ootb-templates.tanzu.vmware.com              0.10.2           Reconcile succeeded
  policy-controller           policy.apps.tanzu.vmware.com                 1.1.2            Reconcile succeeded
  service-bindings            service-bindings.labs.vmware.com             0.8.0            Reconcile succeeded
  source-controller           controller.source.apps.tanzu.vmware.com      0.5.0            Reconcile succeeded
  tap                         tap.tanzu.vmware.com                         1.3.0            Reconcile succeeded
  tap-telemetry               tap-telemetry.tanzu.vmware.com               0.3.1            Reconcile succeeded
  application-service-adapter application-service-adapter.tanzu.vmware.com 1.0.1            Reconcile succeeded
  tekton-pipelines            tekton.tanzu.vmware.com                      0.39.0+tap.2     Reconcile succeeded
```
