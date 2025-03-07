Install K3s
```
curl -sfL https://get.k3s.io | sh -s - --write-kubeconfig-mode=0644
```{{exec}}

Copy the K3s kubeconfig file
```
mkdir -p ~/.kube
cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
```{{exec}}

Install Helm
```
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash`
```{{exec}}

Check that `helm` is setup correctly
```
helm list
