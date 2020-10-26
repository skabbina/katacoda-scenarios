

Create a pod that runs as an unprivileged user.

One way to achieve this is by customizing the base image.

```
FROM alpine:3

RUN addgroup -g 666 -S appgroup && \
    adduser -u 666 -S appuser -G appgroup

USER appuser
```

Another option is to specify restricted privileges in PodSpec's Security Context. 
```
cat <<EOF > unprivileged-app-shell.yaml
apiVersion: v1
kind: Pod
metadata:
  name: unprivileged-app-shell
spec:
  serviceAccountName: demo-sa
  securityContext:
    runAsUser: 666
    runAsGroup: 666
    fsGroup: 666
  containers:
    - name: unprivileged-app-shell
      image: "alpine:3"
      command:
      - /bin/sh
      - -c
      - sleep 600
      securityContext:
        allowPrivilegeEscalation: false
        readOnlyRootFilesystem: true
        capabilities:
          drop:
            - ALL
EOF
kubectl create -f unprivileged-app-shell.yaml
```{{execute}}

Verify the pod is in running state, and then Ctrl-C to break.
`watch 'kubectl get pods'`{{execute}}

Exec into the pod shell and verify the pod user.
```
kubectl exec -it unprivileged-app-shell sh
id
```{{execute}}

Generally, containers run with a default set of capabilities as assigned by the Container Runtime. These defaults can include potentially dangerous capabilities. For instance, CAP_NET_RAW can be used by malicious actors to manipulate network traffic.

Hence, this baseline pod security policy strips away all capabilities including CAP_NET_RAW. This can be verified by a ping, which would be blocked.
`ping www.google.com`{{execute}}

Baseline policy also mounts root file system as read-only. This prevents any malicious program from changing the running container, and inserting backdoors. Further, this also forces immutability in the design.

To verify, try creating a file in /tmp.
`touch /tmp/psp-test`{{execute}}

This fails as the pod's root file system has been mounted as readonly. This constraint may be too restrictive for some designs. In such a case, containers can use store emptyDir volumes for temporary date, and persistent volumes for permanent storage.

Exit the pod shell.
`touch /tmp/psp-test`{{execute}}
