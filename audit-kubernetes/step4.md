

Kubernetes API server and Kubelet daemon are running with root privileges. Hence configuration files must be audited to detect any unauthorised changes.

On this Ubuntu node install audit.

`apt install -y auditd audispd-plugins`{{execute}}

Configure audit policy to watch kubernetes and kubelet configuration file changes

```
cat <<EOF > /etc/audit/rules.d/audit.rules
-w /etc/kubernetes/ -p wa -k kubernetes
-w /var/lib/kubelet/config.yaml -p wa -k kubelet
-w /var/lib/kubelet/pki -p wa -k kubelet
EOF
```{{execute}}

Restart audit service to apply these changes.

`systemctl restart auditd`{{execute}}

Edit Kubelet configuration file.

`sed -i -e 's/\(mode:\).*/\1 Webhook/' /var/lib/kubelet/config.yaml`{{execute}}

And view the audit entry.

`ausearch -k kubectl -ts recent`{{execute}}
