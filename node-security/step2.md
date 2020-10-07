
Time synchronization is important to support time sensitive security mechanisms and also ensures log files have consistent time records across the nodes, which aids in forensic investigations. 

chrony daemon is a different implementation of Network Time Protocol (NTP), compared to legacy ntpd. It can synchronize the time faster and easier to configure. chrony is the default NTP daemon in CenOS 7 and is preferred for VMs which are frequently suspended or otherwise intermittently disconnected from the network.

Ensure chrony daemon is active.

`systemctl status chronyd`{{execute}}

Inspect chrony configuration file to view default time servers configured. Typically production systems do not have access to the internet. In such cases, replace these entries with an appropriate time server. 

`grep -E "^(server|pool)" /etc/chrony.conf`{{execute}}

Verify chrony is synchronizing and the drift in `System Time` is within acceptable limits.

`chronyc tracking`{{execute}}
