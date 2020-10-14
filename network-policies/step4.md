
Similarly configure Ingress policies.
```
cat<<EOF > ingress.yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: block-all-ingress
spec:
  podSelector: {}
  ingress: []
  policyTypes:
    - Ingress
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-yellow-ingress
spec:
  podSelector:
    matchLabels:
     zone: yellow
  ingress:
    - from:
      - podSelector:
          matchLabels:
            zone: orange
      ports:
        - protocol: TCP
          port: 8080
  policyTypes:
    - Ingress
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-green-ingress
spec:
  podSelector:
    matchLabels:
     zone: green
  ingress:
    - from:
      - podSelector:
          matchLabels:
            zone: yellow
      ports:
        - protocol: TCP
          port: 27017
  policyTypes:
    - Ingress
  EOF
  kubectl create -f ingress.yaml
```{{execute}}

Repeat the connectivity tests from **portal tab**.
```
curl -q --connect-timeout 2 app-server:8080
curl -q --connect-timeout 2 database:27017
```{{execute}}

and from the **app-server tab**.
```
curl --connect-timeout 2 portal:80
curl --connect-timeout 2 database:27017
```{{execute}}