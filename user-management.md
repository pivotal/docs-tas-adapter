# User Management

Application Service Adapter users are user identifiers that Kubernetes recognizes in the subject section of its RBAC resources, such as `RoleBindings`. For more information about user subject names in Kubernetes, see the [Referring to subjects](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#referring-to-subjects) section of _Using RBAC Authorization_ and the [Authenticating](https://kubernetes.io/docs/reference/access-authn-authz/authentication/) topic in the Kubernetes project documentation.

Users can be assigned Cloud Foundry roles using the `cf` CLI's [role management commands](https://docs.cloudfoundry.org/adminguide/cli-user-management.html#orgs-spaces) or by directly creating `RoleBinding` resources through the Kubernetes API.

## <a id="eks-user-mapping"></a>AWS IAM user management for EKS
To configure a user for EKS, you must configure the "aws-auth" `ConfigMap` on the EKS cluster to map an IAM resource by ARN to the cluster. Follow the AWS [IAM user and role access documentation](https://docs.aws.amazon.com/eks/latest/userguide/add-user-role.html) for more information.

> **Note**:
The AWS documentation recommends using `eksctl` to edit the ConfigMap.
