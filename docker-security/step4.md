Step 4 - Configure audit.

## Task

Install audit

`yum install -y audit`{{execute}}

Configure audit rules to monitor changes to docker configuration

`cat <<EOF > /etc/audit/rules.d/audit.rules
-w /usr/bin/dockerd -p wa -k docker
-w /etc/docker -p wa -k docker
-w /usr/lib/systemd/system/docker.service -p wa -k docker
-w /usr/bin/docker-containerd -p wa -k docker
-w /var/run/docker.sock -p wa -k docker
EOF`{{execute}}

Start and enable auditd service

systemctl daemon-reload
service auditd restart
systemctl enable auditd
```{{execute}}

Test the audit rules by updating a docker configuration file

`touch /etc/docker/key.json`{{execute}}

Verify the audit entry of the modifiction 

`ausearch -k docker -ts recent`{{execute}}
