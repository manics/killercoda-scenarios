Check the current operating system, you should see Ubuntu 20.04 which is the recommended version of Ubuntu for TLJH.
```
cat /etc/issue.net
```{{exec}}

Install some operating system dependencies including Python 3 and git (may already be installed).
```
sudo apt install -y python3 python3-dev git curl
```{{exec}}

Pick a username for your TLJH administrator, e.g. `admin`, and run the installation script:
```
curl -L https://tljh.jupyter.org/bootstrap.py | sudo -E python3 - --admin admin
```{{exec}}

You should now be able to access JupyterHub at {{TRAFFIC_HOST1_80}}
Login with your administrator username (e.g. `admin`) and any password.
