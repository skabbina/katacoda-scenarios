

Verify kubernetes version and cluster status.
`kubectl version`{{execute}}

Create a minimal audit policy to log all requests at metadata level.

```
cat <<EOF > /etc/kubernetes/audit-policy.yaml
# Log all requests at metadata level.
 apiVersion: audit.k8s.io/v1
 kind: Policy
 rules:
 - level: Metadata
EOF
```{{execute}}

Avoid logging request and response body, as that would expose secrets and other sensitive information. 

Edit API Server configuration.

`vim /etc/kubernetes/manifests/kube-apiserver.yaml`{{execute}}

Add below lines to api-server arguments. Remember to maintain correct indentation.

```
    - --audit-policy-file=/etc/kubernetes/audit-policy.yaml
    - --audit-log-path=/var/log/audit.log
```{{copy}}

Add below lines in volumeMounts section.

```
    - mountPath: /etc/kubernetes/audit-policy.yaml
      name: audit
      readOnly: true
    - mountPath: /var/log/audit.log
      name: audit-log
      readOnly: false
```{{copy}}

Add below lines in volumes section.

```
  - name: audit
    hostPath:
      path: /etc/kubernetes/audit-policy.yaml
      type: File
  - name: audit-log
    hostPath:
      path: /var/log/audit.log
      type: FileOrCreate
```{{copy}}

Once the editor is closed, API Server would restart *automatically* with updated configuration. 

Wait for the API server to restart and then enter `Ctrl-C` to end.
`watch 'ps -aef | grep apiserver'`{{execute}}

Review generated audit logs.

`tail -100 /var/log/audit.log`{{execute}}
