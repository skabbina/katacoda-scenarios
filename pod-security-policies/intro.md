Objective of this scenario is to configure Kubernetes pod security policies.

Pod security policies allow fine grained control over privileges granted to a pod. Defines a set of conditions that all pods must run with in order to be accepted into the system, as well as defaults for the related fields. These policies are not default enabled, but are essential from a cluster security standpoint.

Pod security policies enforce below security controls.   

Pod user and group: Containers are not trust boundaries. A root user inside a container is effectively root on the host node.
Sharing  host namespaces: A pod can access and manipulate host's network, IPC and process namespaces
Docker capabilities: Remove dichotomy between 'root' and 'non-root' users, and allow specific privileges to 'non-root' users.
Seccomp profile: 

* Discretionary access control: Permission to access a resource based on user-id and group id
* Privileged containers: These pods have unrestricted access to all Linux Kernel capabilities and devices
* Allow privilege escalation: Controls whether a process can gain more pivileges then its parent and run setuid/setgid programs
* Linux Capabilities: Removes dichotomy between 'root' and 'non-root' users. Gives a process some, but not all of root privileges
* Seccomp: Linux kernel facility to filter system calls a process is allowed to make
* Security Enhanced Linux (SELinux): Linux kernel level access control policies
* AppArmor: Use program profiles to restrict the capabilities of individual programs
* Read only root filestystem: Mounts the container's root filesystem as read-only and prevents programs from changing the container

For most application pods such privileges are not required. It is recommended to enable a baseline pod security policy to strip away these capabilities. However, in certain cases where workloads require extra privileges, custom policies can be conifigured.
