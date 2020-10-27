
Start the Kubernetes cluster
`launch.sh`{{execute}}

Verify kubernetes version and cluster status. Note that cluster startup may take some time.
`kubectl version`{{execute}}

By default, pods are not scheduled on master nodes. This cluster has only a single node. So remove the master node role taint, to override the default behaviour.
`kubectl taint node controlplane node-role.kubernetes.io/master-`{{execute}}

Kubernetes provides a default namespace, where objects are placed if no namespace is specified for them. Placing objects in default namespace makes isolation of resources and application of RBAC more difficult. So resources must be created in custom namespaces.
`kubectl create namespace demo`{{execute}}

Set the newly created namespace as the default for subsequent CLI commands.
`kubectl config set-context --current --namespace=demo`{{execute}}

Pod security policy control is implemented as an optional, but recommended admission controller. 

Edit API Server configuration to enable to PSP admission controller.

`sed -i '/enable-admission-plugins/s/$/,PodSecurityPolicy/g' /etc/kubernetes/manifests/kube-apiserver.yaml`{{execute}}

API Server would be restarted **automatically** with updated configuration. Wait for the API server to restart and then enter `Ctrl-C` to end.

`watch 'ps -aef | grep apiserver'`{{execute}}

Verify arguments to `enable-admission-plugins` include `PodSecurityPolicy`.

Configure a baseline pod security policy. This strict policy restricts privileged containers, root containers, use of host namespaces and drops all linux capabilities. 
```
cat<<EOF > default-psp.yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: default-psp
spec:
  privileged: false
  allowPrivilegeEscalation: false
  requiredDropCapabilities:
    - ALL
  volumes:
    - 'configMap'
    - 'emptyDir'
    - 'projected'
    - 'secret'
    - 'downwardAPI'
    - 'persistentVolumeClaim'
  hostNetwork: false
  hostIPC: false
  hostPID: false
  readOnlyRootFilesystem: true
  runAsUser:
    rule: 'MustRunAsNonRoot'
  seLinux:
    rule: 'RunAsAny'
  supplementalGroups:
    rule: 'MustRunAs'
    ranges:
      - min: 1
        max: 65535
  fsGroup:
    rule: 'MustRunAs'
    ranges:
      - min: 1
        max: 65535
EOF
kubectl create -f default-psp.yaml
```{{execute}}

Create a Service Account and associate the pod security policy.
```
cat <<EOF > demo-sa.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: demo-sa
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: demo
  name: demo-role
rules:
  - apiGroups:
      - policy
    resources:
      - podsecuritypolicies
    verbs:
      - use
    resourceNames:
      - default-psp
---
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
kubectl create -f demo-sa.yaml
```{{execute}}

Spin up a pod using the service account. Note this pod is runs as root user, and does not comply with the configured policy. 
```
cat <<EOF > app-shell.yaml
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
kubectl create -f app-shell.yaml
```{{execute}}

Check the Pod status. 
`kubectl get pods`{{execute}}

As expected, the PSP Admission Controller blocked the Pod startup.
`kubectl get events`{{execute}}
