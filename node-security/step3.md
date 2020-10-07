
firewalld provides a frontend to Linux netfileter framework via the iptables backend. This supports the concept of network or firewall “zones” to assign a level of trust to a network and its associated connections, interfaces or sources.

Ensure firewalld is running and if not start.

```
systemctl status firewalld
systemctl restart firewalld
```{{execute}}

Kubernetes components use below ports.

| Protocol | Direction | Port Range | Purpose                | Used By              |
|:--------:|:---------:|:----------:|:----------------------:|:--------------------:|
| TCP      | Inbound   | 6443       | Kubernetes API Server  | All                  |
| TCP      | Inbound   | 2379-2380  | etcd server client API | kube-apiserver, etcd |
| TCP      | Inbound   | 10250      | Kubelet API            | Self, Control plane  |
| TCP      | Inbound   | 10251      | kube-scheduler         | Self                 |
| TCP      | Inbound   | 10252      | kube-controller-manager| Self                 |


Configure firewall rules to open these ports.

```
firewall-cmd --zone=public --add-port=6443/tcp --permanent
firewall-cmd --zone=public --add-port=2379-2380/tcp --permanent
firewall-cmd --zone=public --add-port=10250-10252/tcp --permanent
firewall-cmd --reload
```{{execute}}

Verify the firewall rules have been applied.

`firewall-cmd --list-all`{{execute}}
