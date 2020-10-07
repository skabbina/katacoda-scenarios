

Docker daemon runs with root privileges. Hence it is important to audit all docker configuration files, to track any unauthorized changes.

Auditing requires 

`yum install -y audit`{{execute}}

Configure audit rules to monitor changes to docker configuration.

```
cat <<EOF > /etc/audit/rules.d/audit.rules
-w /usr/bin/dockerd -p wa -k docker
-w /etc/docker -p wa -k docker
-w /usr/lib/systemd/system/docker.service -p wa -k docker
-w /usr/bin/docker-containerd -p wa -k docker
-w /var/run/docker.sock -p wa -k docker
EOF
```{{execute}}

Start auditd service and enable to run on boot.

```
systemctl daemon-reload
service auditd restart
systemctl enable auditd
```{{execute}}

Test these audit rules by updating a docker configuration file.

`touch /etc/docker/daemon.json`{{execute}}

Review audit entry of the modifiction.

`ausearch -k docker -ts recent`{{execute}}
