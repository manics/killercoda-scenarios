Download miniconda3, this is a self-contained distribution of Python

```
curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh -b
```{{execute}}

Activate the miniconda environment
`. miniconda3/bin/activate`{{execute}}

And install Ansible
`conda install -y -c conda-forge ansible=3.1`{{execute}}

Open the file `inventory.yml` in an editor and create an Ansible inventory.
This is a list of groups of hosts that we'll manage with Ansible.
Here we have a group called `webservers` that has one host, `localhost`.

<pre class="file" data-filename="inventory.yml" data-target="replace">
webservers:
  hosts:
    localhost:
      ansible_connection: local
</pre>

Now we can check whether the inventory works by attempting to connect using Ansible
`ansible -i inventory.yml -m ping webservers`{{execute}}

Next we can create a playbook.
An Ansible playbook describes what will be done to a server.

First we need to say which servers this playbook will manage, in this case it will manage the host-group called `webservers`.
<pre class="file" data-filename="playbook.yml" data-target="replace">
- hosts: webservers
  become: true

  tasks:

</pre>

Now we'll start adding tasks.
The first task is to add the upstream Nginx mainline yum repository from http://nginx.org/en/linux_packages.html#RHEL-CentOS

<pre class="file" data-filename="playbook.yml" data-target="append">
    - name: Setup Nginx yum repository
      yum_repository:
        baseurl: http://nginx.org/packages/mainline/centos/$releasever/$basearch/
        enabled: true
        name: nginx
        description: nginx mainline repo
        gpgcheck: true
        gpgkey: https://nginx.org/keys/nginx_signing.key

</pre>

Followed by installing Nginx.
<pre class="file" data-filename="playbook.yml" data-target="append">
    - name: Install Nginx
      yum:
        name: nginx-1.19.0
        state: present
</pre>
<!-- Current 1.19.8 -->

Now we can run the playbook, and we should see that Nginx is installed.
`ansible-playbook -i inventory.yml --diff playbook.yml`{{execute}}

Check the version of Nginx
`rpm -q nginx`{{execute}
}
If we run `ansible-playbook` again we'll see that nothing is changed.
Ansible sees that the Nginx yum repository and the Nginx package are already present, so doesn't change anything.
`ansible-playbook -i inventory.yml --diff playbook.yml`{{execute}}

Next we'll want to ensure Nginx is running, and automatically started at boot-up.

<pre class="file" data-filename="playbook.yml" data-target="append">
    - name: Start Nginx, ensure it automatically starts on boot
      service:
        name: nginx
        enabled: true
        state: started
</pre>

`ansible-playbook -i inventory.yml --diff playbook.yml`{{execute}}

Nginx is now running:
https://[[HOST_SUBDOMAIN]]-80-[[KATACODA_HOST]].environments.katacoda.com

Now let's modify the Nginx configuration, and add a webpage.

We'll use `/srv/www/` as the HTML root directory.
Create the directory in Ansible.

<pre class="file" data-filename="playbook.yml" data-target="append">
    - name: Create www directory
      file:
        path: /srv/www
        state: directory
</pre>

And overwrite the default Nginx configuration to server content from this directory.
Create a `default.conf` file.

<pre class="file" data-filename="default.conf" data-target="replace">
server {
    listen       80;
    server_name  localhost;
    location / {
        root /srv/www;
        index index.html index.htm;
    }
}
</pre>

We need to copy this file to the correct location with Ansible.

<pre class="file" data-filename="playbook.yml" data-target="append">
    - name: Configure nginx
      copy:
        dest: /etc/nginx/conf.d/default.conf
        src: default.conf
      notify:
        - restart nginx

</pre>

The final thing we need to do is to ensure Nginx is automatically restarted if the configuration changes.
We can do that by defining a _handler_, and _notifying_ it only if the configuration changes.

<pre class="file" data-filename="playbook.yml" data-target="append">
  handlers:

    - name: restart nginx
      service:
        name: nginx
        state: restarted

</pre>

Now if we run Ansible again we should see that the Nginx configuration is updated.
`ansible-playbook -i inventory.yml --diff playbook.yml`{{execute}}

But if we run it again nothing changes
`ansible-playbook -i inventory.yml --diff playbook.yml`{{execute}}

Go to the webserver URL again, this time you'll see a 404 since there is no content in `/srv/www/`
https://[[HOST_SUBDOMAIN]]-80-[[KATACODA_HOST]].environments.katacoda.com

