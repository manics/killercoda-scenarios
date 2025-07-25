Create a configuration file `config.yaml` in the editor and add the following.

```
# This is the Z2JH configuration file

hub:
  db:
    type: sqlite-memory
#TODO-authentication

proxy:
  service:
    type: NodePort
    nodePorts:
      http: 31080

singleuser:
  storage:
    type: none

```{{copy}}

Add the JupyterHub helm chart repository to the list of repositories Helm knows about
```
helm repo add jupyterhub https://hub.jupyter.org/helm-chart/
helm repo update
```{{exec}}

Now run `helm upgrade` to install JupyterHub into the default namespace.
The `upgrade` command will install JupyterHub if it's not present, or upgrade it if it is.
```
helm upgrade --cleanup-on-fail \
  --install my-jupyterhub jupyterhub/jupyterhub \
  --version 4.1.0 \
  --values config.yaml \
  --wait
```{{exec}}

You should now see JupyterHub at
{{TRAFFIC_HOST1_31080}}

JupyterHub defaults to using the [DummyAuthenticator](https://jupyterhub.readthedocs.io/en/4.0.2/reference/authenticators.html#the-dummy-authenticator), so you can enter _any_ username and password.

You can install JupyterHub into a different namespace such as `jhub` by passing these arguments to helm: `--namespace jhub --create-namespace`.
