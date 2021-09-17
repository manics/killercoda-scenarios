Install some operating system dependencies including Python 3, Nodejs and libraries necessary for installing source packages from PyPI

```
apt-get update -y
apt-get install -y build-essential curl git libcurl4-openssl-dev libssl-dev nodejs python3-venv
```{{execute}}

Fetch the current version of BinderHub and example configuration
```
git clone https://github.com/jupyterhub/binderhub
cd binderhub/testing/local-binder-local-hub
```{{execute}}

Create a virtualenv and install dependencies
```
python3 -mvenv venv
. venv/bin/activate
npm install -g configurable-http-proxy
pip install -r requirements.txt
```{{execute}}

Install BinderHub
```
pip install ../..
```{{execute}}

Since Katacoda requires a special URL to access web services we need to override the JupyterHub access URL that BinderHub returns to the user:
```
sed -i.bak -r -e 's%^JUPYTERHUB_EXTERNAL_URL\s+.+%JUPYTERHUB_EXTERNAL_URL = "https://[[HOST_SUBDOMAIN]]-8000-[[KATACODA_HOST]].environments.katacoda.com/"%' binderhub_config.py
```{{execute}}

Take a look at the JupyterHub and BinderHub configuration files if you want: `binderhub/testing/local-binder-local-hub/jupyterhub_config.py`{{open}} `binderhub/testing/local-binder-local-hub/binderhub_config.py`{{open}}

Run JupyterHub, which will run BinderHub as a managed service:
```
jupyterhub --config=jupyterhub_config.py
```{{execute}}

You should now be able to access BinderHub via JupyterHub at https://[[HOST_SUBDOMAIN]]-8000-[[KATACODA_HOST]].environments.katacoda.com/

Paste a repository into the GitHub repository field, e.g. `https://github.com/binder-examples/requirements`

You can also launch a repository using a build URL: e.g. https://[[HOST_SUBDOMAIN]]-8000-[[KATACODA_HOST]].environments.katacoda.com/services/binder/v2/gh/binder-examples/requirements/HEAD
