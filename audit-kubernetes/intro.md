Objective of this scenario is to configure auditing for Kubernetes.

Kubernetes provides only basic auditing capability at the API Server. This is still useful to detect security issues and is the first step in securing the cluster. Enabling audit logs each API request on each stage of processing i.e `Request Received`, `Response Started` and `Response Complete`. Each step generates an audit event, which is then processed as per audit policy and written to a file or backend such as ElasticSearch for  analysis.

Presently, Kubenernetes does not support auditing kubelet. However, kubelet daemon is running with root privileges. Hence, kubelet configuration files must be audited to detect any unauthorised changes.
