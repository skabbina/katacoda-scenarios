Step 1 - Unintall existing Docker

## Task

CentOS 7 includes a legacy Docker version from CentOS repo.

`docker version`{{execute}}

Uninstall this legacy Docker version.

`rpm -qa | grep docker | xargs yum erase -y /dev/null`{{execute}}

Cleanup Docker storage directories.

`rm -rf /var/lib/docker`{{execute}}

Cleanup Docker configuration files.

`rm -rf /etc/docker`{{execute}}

Cleanup Docker run files.

`rm -rf /etc/docker`{{execute}}
