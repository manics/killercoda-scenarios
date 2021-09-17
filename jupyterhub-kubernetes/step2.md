Create a configuration file `config.yaml`{{open}} in the editor and add the following.

<pre class="file" data-filename="config.yaml" data-target="replace">
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
</pre>

Add the JupyterHub helm chart repository to the list of repositories Helm knows about
```
helm repo add jupyterhub https://jupyterhub.github.io/helm-chart/
helm repo update
```{{execute}}

Now run `helm upgrade` to install JupyterHub.
The `upgrade` command will install JupyterHub if it's not present, or upgrade it if it is.
```
helm upgrade --cleanup-on-fail \
  --install my-jupyterhub jupyterhub/jupyterhub \
  --namespace jhub \
  --create-namespace \
  --version 1.1.3 \
  --values config.yaml \
  --wait
```{{execute}}

You should now see JupyterHub at
https://[[HOST_SUBDOMAIN]]-31080-[[KATACODA_HOST]].environments.katacoda.com

JupyterHub defaults to using the [DummyAuthenticator](https://jupyterhub.readthedocs.io/en/1.3.0/reference/authenticators.html#the-dummy-authenticator), so you can enter _any_ username and password.
