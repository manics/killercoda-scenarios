Check the current operating system, you should see Ubuntu 20.04 which is the recommended version of Ubuntu for TLJH.
```
cat /etc/issue.net
```{{execute}}

Install some operating system dependencies including Python 3 and git (may already be installed).
```
sudo apt install python3 python3-dev git curl
```{{execute}}

Pick a username for your TLJH administrator, e.g. `admin`, and run the installation script:
```
curl -L https://tljh.jupyter.org/bootstrap.py | sudo -E python3 - --admin admin
```{{execute}}

You should now be able to access JupyterHub at https://[[HOST_SUBDOMAIN]]-80-[[KATACODA_HOST]].environments.katacoda.com/
Login with your administrator username (e.g. `admin`) and any password.
