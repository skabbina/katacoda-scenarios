
Network Policies are configured and enforced by the Kunberntes CNI such as `Calico` or `Weavenet`. Default CNI in this environment is `flannel`, which does not support network policies.

Spin-up a single node kubernetes from scratch. This may take few minutes. 
```
kubeadm init --pod-network-cidr=192.168.0.0/16 --kubernetes-version $(kubeadm version -o short) 
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
kubectl taint nodes --all node-role.kubernetes.io/master-
```{{execute}}

Install Calico
```
kubectl create -f https://docs.projectcalico.org/manifests/tigera-operator.yaml
kubectl create -f https://docs.projectcalico.org/manifests/custom-resources.yaml
```{{execute}}

Verify node readiness
`kubectl get nodes`{{execute}}
