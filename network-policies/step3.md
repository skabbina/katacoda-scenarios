
Using `default` service accounts make it harder to configure access policies. It is recommended to use one or more custom service accounts. 

Create a custom service account.
```
cat<<EOF > demo-sa.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: demo-sa
EOF
kubectl create -f demo-sa.yaml
```{{execute}}

Kubernetes Role Based Access Control (RBAC) allows fine-grained access control over cluster resources. Each service accounts must be given carefully assessed RBAC privileges to minimize risk.

Create a Role.
```
cat<<EOF > demo-role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: demo
  name: demo-role
rules:
  - apiGroups: [""]
    resources: ["pods"]
    resourceNames: [""]
    verbs: ["get", "list", "watch"]
EOF
kubectl create -f demo-role.yaml
```{{execute}}

Bind role and the service account.
```
cat<<EOF > demo-binding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: demo-binding
  namespace: demo
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: demo-role
subjects:
  - kind: ServiceAccount
    name: demo-sa
    namespace: demo
EOF
kubectl create -f demo-binding.yaml
```{{execute}}

Create a pod with the custom service account.
```
cat<<EOF > app-shell-sa.yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-shell
spec:
  serviceAccountName: demo-sa
  containers:
    - name: app-shell
      image: "alpine:3"
      command:
        - /bin/sh
        - -c
        - sleep 600
EOF
kubectl create -f app-shell-sa.yaml
```{{execute}}

Verify the pod is started with custom service account.
`kubectl get pods -o yaml | grep service`{{execute}}