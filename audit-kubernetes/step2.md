
API Server provides an insecure port for debugging purposes. This would allow unauthenticated and unencrypted access to master node and take control of the cluster.

Enable insecure access by setting `insecure-port` parameter.

`sed -i -e '/insecure-port/s/=0/=8080/' /etc/kubernetes/manifests/kube-apiserver.yaml`{{execute}}

Wait for the API server to restart and then enter `Ctrl-C` to end.

`watch 'ps -aef | grep apiserver'`{{execute}}

Call Kubernetes API to query pods running in the cluster. These APIs could be used to modify the cluster as well.

`curl -ks http://localhost:8080/api/v1/pods`{{execute}}

Last query would be logged as user `system:unsecured` in the audit log.

`grep unsecured /var/log/audit.log`{{execute}}

Disable insecure port access.

`sed -i -e '/insecure-port/s/=8080/=0/' /etc/kubernetes/manifests/kube-apiserver.yaml`{{execute}}
