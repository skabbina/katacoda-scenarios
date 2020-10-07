Configure Network Firewall

## Task

firewalld provides a frontend to Linux netfileter framework via the iptables backend. This supports the concept of network or firewall “zones” to assign a level of trust to a network and its associated connections, interfaces or sources.

Ensure firewalld is running and if not start.

systemctl status firewalld
systemctl restart firewalld
```{{execute}}

Kubernetes components use below ports.
<pre>
<table>
    <thead>
        <tr>
            <th>Protocol</th>
            <th>Direction</th>
            <th>Port Range</th>
            <th>Purpose</th>
            <th>Used By</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>TCP</td>
            <td>Inbound</td>
            <td>6443*</td>
            <td>Kubernetes API server</td>
            <td>All</td>
        </tr>
        <tr>
            <td>TCP</td>
            <td>Inbound</td>
            <td>2379-2380</td>
            <td>etcd server client API</td>
            <td>kube-apiserver, etcd</td>
        </tr>
        <tr>
            <td>TCP</td>
            <td>Inbound</td>
            <td>10250</td>
            <td>Kubelet API</td>
            <td>Self, Control plane</td>
        </tr>
        <tr>
            <td>TCP</td>
            <td>Inbound</td>
            <td>10251</td>
            <td>kube-scheduler</td>
            <td>Self</td>
        </tr>
        <tr>
            <td>TCP</td>
            <td>Inbound</td>
            <td>10252</td>
            <td>kube-controller-manager</td>
            <td>Self</td>
        </tr>
    </tbody>
</table>
<pre>

Configure firewall rules to open these ports.
firewall-cmd --zone=public --add-port=6443/tcp --permanent
firewall-cmd --zone=public --add-port=2379-2380/tcp --permanent
firewall-cmd --zone=public --add-port=10250-10252/tcp --permanent
firewall-cmd --reload
```{{execute}}

Verify the firewall rules
`firewall-cmd --list-all`{{execute}}
