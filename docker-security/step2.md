Step 2 - Configure Docker-CE Yum Repo.

## Task

Install yum-utils package

`yum install -y yum-utils`{{execute}}

Install Docker-CE repo

`yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`{{execute}}

