# Using Kubernetes API to create Cloud Foundry resources

Application Service Adapter is backed entirely by Kubernetes custom resources,
enabling operators to manage organizations, spaces, and roles declaratively
through the Kubernetes API. For more information, see [Custom
Resources](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)
in the Kubernetes documentation. Operators can use any Kubernetes API client
(such as kubectl, `kapp`, and so on) to manage resources. VMware has documented
examples using both clients. The following examples assume the default value of
`cf` for `$ROOT_NAMESPACE`.

## Using kubectl to manage resources

### Creating orgs

Use a `CFOrg` custom resource to create an organization. For example:

```sh
cat <<EOF | kubectl apply -f -
apiVersion: korifi.cloudfoundry.org/v1alpha1
kind: CFOrg
metadata:
  name: my-org-guid
  namespace: cf
spec:
  displayName: my-org
EOF

kubectl wait --for=condition=ready cforg/my-org-guid -n cf
```

> **Note** `CFOrg` objects can only be created within the `$ROOT_NAMESPACE`.

After the `CFOrg` is `ready`, you can proceed to create spaces or grant users
access to this organization.

### Creating spaces

Use a `CFSpace` custom resource to create a space. For example:

```sh
cat <<EOF | kubectl apply -f -
apiVersion: korifi.cloudfoundry.org/v1alpha1
kind: CFSpace
metadata:
  name: my-space-guid
  namespace: my-org-guid
spec:
  displayName: my-space
EOF

kubectl wait --for=condition=ready cfspace/my-space-guid -n my-org-guid
```

> **Note** `CFSpace` objects can only be created within a `CFOrg` namespace.

After the `CFSpace` is `ready`, you can proceed to grant users access to this
space.

### Grant users or service accounts access to organizations and spaces

Application Service Adapter relies on Kubernetes role-based  access control
(RBAC), `Roles`, `ClusterRoles`, `RoleBindings`, for authentication and
authorization. On an Application Service Adapter cluster, [Cloud Foundry
roles](https://docs.cloudfoundry.org/concepts/roles.html), such as `Admin` or
`SpaceDeveloper`, are represented as `ClusterRoles`. Users or ServiceAccounts
are granted access by assigning them these roles through namespace-scoped
`RoleBindings`.

> **Note** As of v1.2, Application Service Adapter only supports the Admin,
> OrgUser, and SpaceDeveloper roles.

#### Grant a user access to the Application Service Adapter API

All Application Service Adapter users must have a `RoleBinding` in the
`$ROOT_NAMESPACE` for the `ClusterRole` `korifi-controllers-root-namespace-user`
to access the API.

This is required to:

- Verify whether a user is allowed access to Application Service Adapter.
- Allow registered users to list `CFDomains`, `CFOrgs`, and `BuilderInfos`
  resources. These are stored in the `$ROOT_NAMESPACE` and must be listable by
  all registered users with any roles.

Here is an example command to create this role binding for a user named
my-cf-user:

```sh
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: my-cf-user-korifi-access
  namespace: cf
  labels:
    cloudfoundry.org/role-guid: my-cf-user-korifi-access-guid
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: korifi-controllers-root-namespace-user
subjects:
  - kind: User
    name: my-cf-user
EOF
```

#### Grant a user admin-level access

In Kubernetes, `RoleBindings` are namespace-scoped, which means they are only
valid within the namespace they are created in. In the case of an admin user, a
rolebindings to the `korifi-contollers-admin` `ClusterRole` is required in the
`$ROOT_NAMESPACE` and in the namespaces for all current and future orgs and
spaces. To make this easier for operators, VMware has the
`cloudfoundry.org/propagate-cf-role=true` annotation for rolebindings in the
`$ROOT_NAMESPACE`. This annotation propagates them into the namespaces that
represent all orgs and spaces.

Here is an example of assigning the admin role to the user my-cf-user:

```sh
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: my-cf-user-admin
  namespace: cf
  labels:
    cloudfoundry.org/role-guid: my-cf-user-admin-guid
  annotations:
    cloudfoundry.org/propagate-cf-role: "true"
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: korifi-controllers-admin
subjects:
  - kind: User
    name: my-cf-user
EOF
```

#### Granting a user space developer access

If you only want to grant a user `SpaceDeveloper` access, you can instead create
a `RoleBinding` to the `ClusterRole` `korifi-controllers-space-developer` in a
space's namespace.

Here is an example of assigning the `SpaceDeveloper` Cloud Foundry role to the user
my-cf-user in the space with the GUID my-space-guid:

```sh
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: my-cf-user-space-developer
  namespace: my-space-guid
  labels:
    cloudfoundry.org/role-guid: my-cf-user-space-developer-guid
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: korifi-controllers-space-developer
subjects:
  - kind: User
    name: my-cf-user
EOF
```

All non-admin users must also have the `OrgUser` role in the org that contains
the space. This is represented by a `RoleBinding` to the `ClusterRole`
`korifi-controllers-organization-user` in the org's namespace.

Here is an example of assigning the `OrgUser` Cloud Foundry role to the user my-cf-user in
the org with the GUID my-org-guid:

```sh
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: my-cf-user-org-user
  namespace: my-org-guid
  labels:
    cloudfoundry.org/role-guid: my-cf-user-org-user-guid
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: korifi-controllers-organization-user
subjects:
  - kind: User
    name: my-cf-user
EOF
```

> **Note** When configuring a `RoleBinding`, it is possible to specify multiple
> `subjects` for a single binding. However, to maintain compatibility with CF
> CLI, you must maintain a 1:1 ratio between `RoleBindings` and
> `Users`/`ServiceAccounts`.

#### Granting roles to service accounts

Application Service Adapter supports granting roles to both users and service
accounts. To grant a role to a service account, follow the earlier instructions
for granting a role to a user but change the `subjects` field to specify a
`ServiceAccount`.

For example, here is how to assign the `OrgUser` Cloud Foundry role to the service
account my-service-account in the namespace my-service-account-namespace:

```sh
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: my-service-account-org-user
  namespace: my-org-guid
  labels:
    cloudfoundry.org/role-guid: my-service-account-org-user-guid
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: korifi-controllers-organization-user
subjects:
  - kind: ServiceAccount
    name: my-service-account
    namespace: my-service-account-namespace
EOF
```

## Using `kapp` to declaratively apply all resources in a single `yaml`

[`kapp`](https://carvel.dev/kapp/) is an open source Kubernetes deployment tool
that provides a streamlined way to manage and deploy Kubernetes applications by
using declarative configuration. See the `kapp`
[documentation](https://carvel.dev/kapp/docs/v0.54.0/) for installation and
usage instructions.

Here is an example of creating a `CFOrg` `CFSpace` and granting the user
my-cf-user the Cloud Foundry `Admin` role in a single command:

```sh
cat <<EOF | kapp deploy -a my-config -y -f -
---
apiVersion: kapp.k14s.io/v1alpha1
kind: Config
metadata:
  name: kapp-config
  annotations: {}

minimumRequiredVersion: 0.29.0

waitRules:
- supportsObservedGeneration: false
  conditionMatchers:
  - type: Ready
    status: "False"
    failure: true
  - type: Ready
    status: "True"
    success: true
  resourceMatchers:
  - apiVersionKindMatcher: {apiVersion: korifi.cloudfoundry.org/v1alpha1, kind: CFOrg}
  - apiVersionKindMatcher: {apiVersion: korifi.cloudfoundry.org/v1alpha1, kind: CFSpace}

---
apiVersion: korifi.cloudfoundry.org/v1alpha1
kind: CFOrg
metadata:
  name: my-org-guid
  namespace: cf
  annotations:
    kapp.k14s.io/change-group: "cforgs"
spec:
  displayName: my-org

---
apiVersion: korifi.cloudfoundry.org/v1alpha1
kind: CFSpace
metadata:
  name: my-space-guid
  namespace: my-org-guid
  annotations:
    kapp.k14s.io/change-group: "cfspaces"
    kapp.k14s.io/change-rule: "upsert after upserting cforgs"
spec:
  displayName: my-space

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: my-cf-user-korifi-access
  namespace: cf
  labels:
    cloudfoundry.org/role-guid: my-cf-user-korifi-access-guid
  annotations:
    kapp.k14s.io/change-rule: "upsert after upserting cfspaces"
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: korifi-controllers-root-namespace-user
subjects:
  - kind: User
    name: my-cf-user

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: my-cf-user-admin
  namespace: cf
  labels:
    cloudfoundry.org/role-guid: my-cf-user-admin-guid
  annotations:
    cloudfoundry.org/propagate-cf-role: "true"
    kapp.k14s.io/change-rule: "upsert after upserting cfspaces"
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: korifi-controllers-admin
subjects:
  - kind: User
    name: my-cf-user
EOF
```
