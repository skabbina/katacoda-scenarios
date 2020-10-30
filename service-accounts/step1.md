
Start the Kubernetes cluster.
`launch.sh`{{execute}}

Verify kubernetes version and cluster status. Note that cluster startup may take some time.
`kubectl version`{{execute}}

By default, pods are not scheduled on master nodes. This cluster has only a single node. So remove the master node role taint, to override the default behaviour.
`kubectl taint node controlplane node-role.kubernetes.io/master-`{{execute}}

Kubernetes provides a default namespace, where objects are placed if no namespace is specified for them. Placing objects in default namespace makes isolation of resources and application of RBAC more difficult. So resources must be created in custom namespaces.
`kubectl create namespace demo`{{execute}}

Set the newly created namespace as the default for subsequent CLI commands.
`kubectl config set-context --current --namespace=demo`{{execute}}

Deploy a pod to this namespace. This minimal pod would startup and sleep for 10minutes -- sufficient enough time for our testing. Once the Pod exits the Kubernetes would restart the Pod, but initializtion steps would need to be repeated.
```
cat <<EOF > app-shell.yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-shell
spec:
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

Since no service account was specified for this pod, Kubernetes assigns this to `default` service account.
`kubectl get pods -o yaml | grep service`{{execute}}

Note that each namespace would have a separate `default` service account.
`kubectl get -A serviceaccount | grep default`{{execute}}

Kubernetes also auto mounts the service account's authentication token on the pod. Login to the pod and check.
```
kubectl exec -it app-shell sh
cd /var/run/secrets/kubernetes.io/serviceaccount
ls -l
cat namespace
cat token
```{{execute}}

Pod can use this token to access the Kubernetes API. Cluster's API Server URL are available to all pods through environment variables.
```
echo $KUBERNETES_SERVICE_HOST
echo $KUBERNETES_SERVICE_PORT
```{{execute}}

Use curl to access namespace and cluster resources.
```
apk add curl
NS=$(cat namespace)
TOKEN=$(cat token)
curl -ks -H "Authorization: Bearer $TOKEN" https://${KUBERNETES_SERVICE_HOST}:${KUBERNETES_SERVICE_PORT}/api/v1/namespaces/$NS/secrets
curl -ks -H "Authorization: Bearer $TOKEN" https://${KUBERNETES_SERVICE_HOST}:${KUBERNETES_SERVICE_PORT}/api/v1/nodes
```{{execute}}

Exit the pod shell.
`exit`{{execute}}

And delete the pod.
```
kubectl delete -f app-shell.yaml
```{{execute}}



