# Upgrading Application Service Adapter

This document describes how to upgrade Application Service Adapter for VMware Tanzu Application Platform.

You can perform a fresh installation of the Application Service Adapter by following the instructions in [Installing Application Service Adapter](install.md).

When upgrading to a new major or minor version of Application Service Adapter, please see that versions' documentation for version-specific configuration and upgrade instructions.

You can find new patch versions of the Application Service Adapter on the TanzuNet product page directly, or [sign up to receive email alerts](https://network.tanzu.vmware.com/docs/faq#alerts) when the product is updated.

## <a id='prereqs'></a> Prerequisites

Before you upgrade Application Service Adapter:

- Verify that you meet all the [prerequisites](install-prerequisites.md) of the target Tanzu Application Service Adapter for TAP version. If the target Tanzu Application version does not support your existing Kubernetes version, VMware recommends upgrading to a supported version before proceeding with the upgrade.
- [Install or update the Tanzu CLI and plugins](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.2/tap/GUID-install-tanzu-cli.html#install-or-update-the-tanzu-cli-and-plugins-3)
- For information about installing or updating the Tanzu CLI and plug-ins, see [Install or update the Tanzu CLI and plugins](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.2/tap/GUID-install-tanzu-cli.html#install-or-update-the-tanzu-cli-and-plugins-3).
- Verify all packages are reconciled by running `tanzu package installed list -A`.
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

## <a id="upgrade-tap"></a> Upgrade Application Service Adapter

To upgrade, run:


```bash
tanzu package installed update tas-adapter \
  --package-name application-service-adapter.tanzu.vmware.com \
  --version "${TAS_ADAPTER_VERSION}" \
  --values-file tas-adapter-values.yaml \
  --namespace tap-install
```

Where `TAS_ADAPTER_VERSION` is the target revision of Application Service Adapter you are migrating to.

## <a id="verify"></a> Verify the upgrade

> **Important** Run the following command in the directory where the `tas-adapter-values.yaml` file resides.

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