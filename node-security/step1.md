
All bets are off, if the malicious user is able to access to the cluster node. Insecure remote access mechanisms like telnet and rsh must be disabled, and ssh must be used to prevent session hijacking and sniffing of sensitive data off the network. This scenario step configures ssh settings.

Verify sshd is running.

`systemctl status sshd`{{execute}}

Disallowing root logins over SSH requires system admins to authenticate using their own individual account, then escalating to root via sudo or su. This in turn limits opportunity for non-repudiation and provides a clear audit trail in the event of a security incident.

`echo 'PermitRootLogin no' >> /etc/ssh/sshd_config`{{execute}}

Only specific, trusted user accounts must be allowed to ssh into the system. ssh provides four configuration options viz. AllowUsers, AllowGroups, DenyUsers and DenyGroups. Combination of these options can be used.

```
groupadd admins
echo 'AllowGroups admins' >> /etc/ssh/sshd_config
```{{execute}}

Set session idle timeout to reduce the risk of unauthorized users accessing to another user's ssh session.

`echo 'ClientAliveInterval 300' >> /etc/ssh/sshd_config`{{execute}}

Disable X11 forwarding as there is no need to use X11 application on Kubernetes nodes.

`echo 'X11Forwarding no' >> /etc/ssh/sshd_config`{{execute}}

Reload sshd configurations

`systemctl reload sshd`{{execute}}

Verify configurations are reloaded. Note: due to Katacoda restrictions these changes will *not* be applied in this environment.

`sshd -T`{{execute}}
