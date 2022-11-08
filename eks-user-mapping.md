# EKS User Mapping

To configure a user for EKS, you must configure the `aws-auth` ConfigMap on the EKS cluster to
map an IAM resource by ARN to the cluster according to the AWS
 [docs](https://docs.aws.amazon.com/eks/latest/userguide/add-user-role.html).

> **Note**:
The AWS docs recommend using `eksctl` to edit the ConfigMap.