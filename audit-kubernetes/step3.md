
Kubelet daemon provides another entry point to the Kubernetes cluster. Kubelet configutation can be modified to enable unauthenticated access to node data.

Edit kubelet configuration file.

`/var/lib/kubelet/config.yaml`{{edit}}

```
authentication:
  anonymous:
    enabled: true
```{{copy}}

```
authorization:
  mode: AlwaysAllow
```{{copy}}

Restart kubelet daemon.

`systemctl restart kubelet`{{execute}}

Call kubelet API to query pods running on the node. 

curl -ks https://localhost:10250/pods
