
Deploy a set of demo pods and services. This is a minimal deployment to demonstrate and 

Deploy a nginx portal. Since exteral facing, label as `orange zone`
```
cat<<EOF > portal.yaml
apiVersion: v1
kind: Pod
metadata:
  name: portal
  labels:
    app: portal
    zone: orange
spec:
  containers:
    - name: portal
      image: "nginx"
      ports:
        - name: http
          containerPort: 80
          protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: portal
spec:
  type: NodePort
  selector:
    app: portal
  ports:
    - name: http
      port: 80
      nodePort: 30080
      protocol: TCP
EOF
kubectl create -f portal.yaml
```{{execute}}

Deploy a mongo database, label as `green zone`.
cat<<EOF > database.yaml
apiVersion: v1
kind: Pod
metadata:
  name: database
  labels:
    app: database
    zone: green
spec:
  containers:
    - name: database
      image: "mongo:3"
      ports:
        - name: http
          containerPort: 27017
          protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: database
spec:
  selector:
    app: database
  ports:
    - name: db-port
      port: 27017
      protocol: TCP
EOF
kubectl create -f database.yaml
```{{execute}}

Finally deploy a `mock` application server pod. This runs netcat tool to respond to HTTP requests. Mark this as `yellow zone`.
```
cat<<EOF > app-server.yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-server
  labels:
    app: app-server
    zone: yellow
spec:
  containers:
    - name: app-server
      image: "alpine:3"
      command:
        - /bin/sh
        - -c
        - while true; do
            echo -e "HTTP/1.1 200 OK\r\n\r\n$(date)" | nc -vl -p 8080;
          done
      ports:
        - name: app-port
          containerPort: 8080
          protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: app-server
spec:
  selector:
    app: app-server
  ports:
    - name: http
      port: 8080
      protocol: TCP
EOF
kubectl create -f app-server.yaml
```{{execute}}

