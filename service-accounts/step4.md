
Note that deleting a service account would not stop the pods already started with that account.
`kubectl delete serviceaccount demo-sa`

Now try accessing the with the deleted service account credentials. This would fail as unauthenticated.
```
kubectl exec -it app-shell sh
cd /var/run/secrets/kubernetes.io/serviceaccount
apk add curl
NS=$(cat namespace)
TOKEN=$(cat token)
curl -ks -H "Authorization: Bearer $TOKEN" https://${KUBERNETES_SERVICE_HOST}:${KUBERNETES_SERVICE_PORT}/api/v1/namespaces/$NS/secrets
```{{execute}}

However, service account status can be disabled. API Server would only verify the token validity, but does not check the service account status with backend etcd. This is must be disabled in production.

Edit API Server configuration. 
`vim /etc/kubernetes/manifests/kube-apiserver.yaml`{{execute}}

Add below line to api-server arguments. Remember to maintain correct indentation.
`    - --service-account-lookup=false`{{copy}}

Once the editor is closed, API Server would restart automatically with updated configuration. 

Wait for the API server to restart and then enter `Ctrl-C` to end.
`watch 'ps -aef | grep apiserver'`{{execute}}

Again, attempt to access the APIs.
```
kubectl exec -it app-shell sh
cd /var/run/secrets/kubernetes.io/serviceaccount
apk add curl
NS=$(cat namespace)
TOKEN=$(cat token)
curl -ks -H "Authorization: Bearer $TOKEN" https://${KUBERNETES_SERVICE_HOST}:${KUBERNETES_SERVICE_PORT}/api/v1/namespaces/$NS/secrets
```{{execute}}
