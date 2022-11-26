Install some operating system dependencies including Python 3, Nodejs and libraries necessary for installing source packages from PyPI

```
apt-get update -y
apt-get install -y build-essential curl git libcurl4-openssl-dev libssl-dev nodejs npm python3-venv
```{{exec}}

Fetch the current version of BinderHub and example configuration
```
git clone https://github.com/jupyterhub/binderhub
cd binderhub/testing/local-binder-local-hub
```{{exec}}

Create a virtualenv and install dependencies
```
python3 -mvenv venv
. venv/bin/activate
npm install -g configurable-http-proxy
pip install -r requirements.txt
```{{exec}}

Install BinderHub
```
pip install ../..
```{{exec}}

Since Katacoda requires a special URL to access web services we need to override the JupyterHub access URL that BinderHub returns to the user by setting an environment variable used in the configuration files:
```
export JUPYTERHUB_EXTERNAL_URL="{{TRAFFIC_HOST1_8000}}"
```{{exec}}

Take a look at the JupyterHub and BinderHub configuration files if you want: `binderhub/testing/local-binder-local-hub/jupyterhub_config.py` `binderhub/testing/local-binder-local-hub/binderhub_config.py`

Run JupyterHub, which will run BinderHub as a managed service:
```
jupyterhub --config=jupyterhub_config.py
```{{exec}}

You should now be able to access BinderHub via JupyterHub at
{{TRAFFIC_HOST1_8000}}

Paste a repository into the GitHub repository field, e.g. `https://github.com/binder-examples/requirements`

You can also launch a repository using a build URL: e.g.
{{TRAFFIC_HOST1_8000}}/services/binder/v2/gh/binder-examples/requirements/HEAD
