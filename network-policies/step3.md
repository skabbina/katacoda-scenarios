
Pods are deployed `non-isolated` i.e. do not have any network policies applied.

Use the `+` to start a new terminal tab. Henceforth, let's refer to this as `portal tab`. Login into `portal` pod.
`kubectl exec -it portal bash`{{execute}}

Verify pod can communicate with any other pod and with external services.
```
curl app-server:8080
curl database:27017
curl www.google.com
```{{execute}}

Similarly open another terminal, henceforth `app-server tab`. Login to `app-server` pod.
`kubectl exec -it app-server sh`{{execute}}

Install curl and test connectivity to other pods.
```
apk add curl
curl portal:80
curl database:27017
curl www.google.com
```{{execute}}

Switch back to the default initial terminal tab.

Apply a `block all` egress policy. This would ensure any new pod would not non-isolated and bypass the restrictions.
```
cat<<EOF > block-all-egress.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: block-all-egress
spec:
  podSelector: {}
  egress:
    - to:
      ports:
        - protocol: TCP
          port: 53
        - protocol: UDP
          port: 53
  policyTypes:
    - Egress
EOF
kubectl create -f block-all-egress.yaml
```{{execute}}

Now selectively allow communication between different zones.
```
cat<<EOF > allow-egress.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-orange-egress
spec:
  podSelector:
    matchLabels:
      zone: orange
  egress:
    - to:
        - podSelector:
            matchLabels:
              zone: yellow
      ports:
        - protocol: TCP
          port: 8080
  policyTypes:
    - Egress
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-yellow-egress
spec:
  podSelector:
    matchLabels:
      zone: yellow
  egress:
    - to:
        - podSelector:
            matchLabels:
              zone: green
      ports:
        - protocol: TCP
          port: 27017
  policyTypes:
    - Egress
EOF
kubectl create -f allow-egress.yaml
```{{execute}}

Now from the portal tab rerun commands to verify.
```
curl --connect-timeout 2 app-server:8080
curl --connect-timeout 2 database:27017
curl --connect-timeout 2 www.google.com > /dev/null
```{{execute}}

Similarly from app-server tab.
```
curl --connect-timeout 2 portal:80
curl --connect-timeout 2 database:27017
curl --connect-timeout 2 www.google.com > /dev/null
```{{execute}}
