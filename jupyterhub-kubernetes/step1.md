Check the Kubernetes cluster is working
```
kubectl get nodes
```{{exec}}

You should see something like
```
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   14d   v1.32.1
```
Install Helm 3 by running the installation script
`curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash`{{exec}}
See https://helm.sh/docs/intro/install/ for more information.

Check that `helm` is setup correctly
```
helm list
```{{exec}}
Should show an empty list.
