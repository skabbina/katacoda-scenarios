Objective of this scenario is to secure Kubernetes service account related configurations.

Kubernetes distinguishes between the concept of user accounts and a service accounts. User accounts are for humans and are at cluster level. Whereas service accounts are namespaced and are for processes to run pods. Kubernetes provides resource APIs to create and manage the service accounts. While user accounts are not managed by Kubernetes.

Kubernetes provides RBAC policies to grant scoped permissions to accees cluster resources. Service accounts can be assigned specific roles and given access to required resources. A fine-grained policies provide greater security, but require more effort to manage. Broader grants are easier to manage but can give unnecessary (and potentially escalating) API access to service accounts.
