
Certain Pods may require to be run as root or may need extra capabilities. A custom pod security policy can provide required privileges. Such policies must be carefully crafted to ensure only the minimum required privileges are granted.

For instance, nginx container must be run as root user. nginx also binds to privilged port 80. Any port less than 1024 requires CAP_NET_BIND_SERVICES capability.

Below given policy allows pods to run as root users, and also allows pods to retain default capabilities.
```
cat<<EOF > custom-psp.yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: custom-psp
spec:
  privileged: false
  allowPrivilegeEscalation: false
  volumes:
    - 'configMap'
    - 'emptyDir'
    - 'projected'
    - 'secret'
    - 'downwardAPI'
    - 'persistentVolumeClaim'
  hostNetwork: false
  hostIPC: false
  hostPID: false
  readOnlyRootFilesystem: false
  runAsUser:
    rule: 'RunAsAny'
  seLinux:
    rule: 'RunAsAny'
  supplementalGroups:
    rule: 'MustRunAs'
    ranges:
      - min: 0
        max: 65535
  fsGroup:
    rule: 'MustRunAs'
    ranges:
      - min: 0
        max: 65535
EOF
kubectl create -f custom-psp.yaml
```{{execute}}

Update the role with the new policy.
```
cat<<EOF > demo-role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: demo
  name: demo-role
rules:
  - apiGroups:
      - policy
    resources:
      - podsecuritypolicies
    verbs:
      - use
    resourceNames:
      - custom-psp
EOF
kubectl replace -f demo-role.yaml
```{{execute}}

Start a nginx pod and bind to privilged port.
```
cat<<EOF > portal.yaml
apiVersion: v1
kind: Pod
metadata:
  name: portal
  labels:
    app: portal
spec:
  serviceAccountName: demo-sa
  containers:
    - name: root-portal
      image: "nginx"
      ports:
        - name: http
          containerPort: 80
          protocol: TCP
EOF
kubectl create -f portal.yaml
```{{execute}}

Verify the pod is in running state, and then Ctrl-C to break.
`watch 'kubectl get pods'`{{execute}}

Exec into the pod shell and verify the pod user.
```
kubectl exec -it portal sh
id
touch /tmp/psp-test
```{{execute}}
