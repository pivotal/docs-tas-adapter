# User management

This topic describes the management of users in an Application Service Adapter
installation.

Application Service Adapter users are user identifiers that Kubernetes
recognizes in the subject section of its role-based access control (RBAC)
resources, such as RoleBindings. For more information about user subject names
in Kubernetes, see the [Referring to
subjects](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#referring-to-subjects)
section of _Using RBAC Authorization_ and the
[Authenticating](https://kubernetes.io/docs/reference/access-authn-authz/authentication/)
topic in the Kubernetes project documentation.

You can assign users Cloud Foundry roles by using the role management commands
of the cf CLI or by directly creating RoleBinding resources through the
Kubernetes API. For more information, see the [Cloud Foundry
documentation](https://docs.cloudfoundry.org/adminguide/cli-user-management.html#orgs-spaces).

## <a id="aws-iam-user-management-eks"></a>AWS IAM user management for EKS
To configure an AWS IAM user for an Elastic Kubernetes Service (EKS) cluster,
you must configure the `aws-auth` ConfigMap on the EKS cluster to map IAM
resources by ARN to the cluster. For more information, see the AWS [IAM user and role access
documentation](https://docs.aws.amazon.com/eks/latest/userguide/add-user-role.html).

> **Note** The AWS documentation recommends using `eksctl` to edit the
> ConfigMap.
