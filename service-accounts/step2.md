
Once a pod compromised, malicious actor can access the API server and attack rest of the cluster. 

Most pods do not need to access the API server. So not mounting the service account tokens would reduce the risk.

To disable automount, update service account configuration to set `automountServiceAccountToken: false`
```
cat <<EOF > disable-default-sa.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: default
  namespace: demo
automountServiceAccountToken: false
EOF
kubectl patch serviceaccount/default -p "$(cat disable-default-sa.yaml)"
```{{execute}}

Recreate the pod, and verify `default` service account's tokens are not mounted.
```
kubectl create -f app-shell.yaml
kubectl get pods -o yaml | grep service
```{{execute}}

Now APIs aceess would be rejected as unauthenticated. Also the pod does not have any knowledge of the namespace it is running in.
```
ls -l /var/run/secrets/kubernetes.io/serviceaccount
kubectl exec -it app-shell sh
apk add curl
curl -ks https://${KUBERNETES_SERVICE_HOST}:${KUBERNETES_SERVICE_PORT}/api/v1/nodes
```{{execute}}

Exit the pod shell.
`exit`{{execute}}

And delete the pod.
```
kubectl delete -f app-shell.yaml
```{{execute}}