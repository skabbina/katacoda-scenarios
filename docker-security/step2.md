
Install a recent docker version, compatible with targeted Kubenetes version. 

Install yum-utils and other prerequisite packages.

`yum install -y yum-utils device-mapper-persistent-data lvm2`{{execute}}

Add the Docker Community (or Enterprise) Edition repo.

`yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`{{execute}}

View available versions Docker Engine in the Docker-CE repo. 

`repoquery --show-duplicates docker-ce`{{execute}}

Install Docker Engine v18.06 compatible with Kubernetes v1.18.

`yum install -y docker-ce-18.06.3.ce-3.el7.x86_64`{{execute}}

Configure Docker daemon to use more efficient overlay2 filestystem. Also, limit container logs to avoid filling up disk space on the host.

'''
mkdir -p /etc/docker
cat <<EOF > /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m",
  },
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
EOF
'''

Start the Docker Enginer systemd service, and enable to run on boot.

```
systemctl daemon-reload
systemctl start docker
systemctl enable docker
```{{execute}}