Check the Kubernetes cluster is working
```
kubectl get nodes
```{{execute}}

If you see an error the cluster may still be initialising.
Wait a minute or two and try again.
```
kubectl get nodes
```{{execute}}

Install Helm 3 by running the installation script
`curl https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash`{{execute}}
See https://helm.sh/docs/intro/install/ for more information.

Check that `helm` is setup correctly
```
helm list
```{{execute}}
Should show an empty list.
