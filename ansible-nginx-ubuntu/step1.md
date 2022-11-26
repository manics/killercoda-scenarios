Download miniconda3, this is a self-contained distribution of Python

```
curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh -b
```{{exec}}

Activate the miniconda environment
`. ~/miniconda3/bin/activate`{{exec}}

And install Ansible
`conda install -y -c conda-forge ansible=6`{{exec}}
