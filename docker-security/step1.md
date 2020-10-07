
CentOS 7 includes a legacy version of Docker. Using legacy software versions pose a critical security risk, as the vulnerabilities and exploits are widely published.

`docker version`{{execute}}

Uninstall this legacy Docker Engine.

`rpm -qa | grep docker | xargs yum erase -y`{{execute}}

Cleanup Docker storage directories.

`rm -rf /var/lib/docker`{{execute}}

Cleanup Docker configuration files.

`rm -rf /etc/docker`{{execute}}

Cleanup Docker run files.

`rm -rf /etc/docker`{{execute}}
