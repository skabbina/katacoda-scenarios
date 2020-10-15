
Kubelet daemons running on each cluster node, provide another entry point to the Kubernetes cluster. Kubelet configutation can be modified to enable unauthenticated access to node data. 

Edit kubelet configuration file.

`vim /var/lib/kubelet/config.yaml`{{execute}}

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

`curl -ks https://localhost:10250/pods`{{execute}}
