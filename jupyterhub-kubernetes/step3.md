Now let's customise the JupyterHub deployment by modifying the singleuser server.

Change the default to JupyterLab by adding

<pre class="file" data-filename="config.yaml" data-target="append">
  defaultUrl: /lab
</pre>

under the `singleuser` section of `config.yaml`{{open}}.

Let's also switch to the [NativeAuthenticator](https://native-authenticator.readthedocs.io/) and allow people to sign up [without needing to be approved](https://native-authenticator.readthedocs.io/en/stable/options.html#open-signup).

Add the following to the `hub` section of `config.yaml`{{open}}.

<pre class="file" data-filename="config.yaml" data-target="insert" data-marker="#TODO-authentication">
  config:
    JupyterHub:
      authenticator_class: nativeauthenticator.NativeAuthenticator
    NativeAuthenticator:
      open_signup: true
</pre>

Now update your deployment.

```
helm upgrade --cleanup-on-fail \
  --install my-jupyterhub jupyterhub/jupyterhub \
  --namespace jhub \
  --create-namespace \
  --version 3.2.1 \
  --values config.yaml \
  --wait
```{{execute}}

Go to {{TRAFFIC_HOST1_31080}}, create an account, and login.
