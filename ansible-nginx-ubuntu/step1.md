Download miniconda3, this is a self-contained distribution of Python

```
curl -O https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh -b
```{{execute}}

Activate the miniconda environment
`. ~/miniconda3/bin/activate`{{execute}}

And install Ansible
`conda install -y -c conda-forge ansible=3.1`{{execute}}

Open the file `inventory.yml`{{open}} in an editor and create an Ansible inventory.
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
Open `playbook.yml`{{open}} and add this

<pre class="file" data-filename="playbook.yml" data-target="replace">
- hosts: webservers
  become: true

  tasks:

</pre>

Now we'll start adding tasks.
The first task is to add the upstream Nginx mainline apt repository and signing key from http://nginx.org/en/linux_packages.html#Ubuntu

<pre class="file" data-filename="playbook.yml" data-target="append">
    - name: Import Nginx repository key
      apt_key:
        id: 573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62
        url: https://nginx.org/keys/nginx_signing.key
        state: present

    - name: Setup Nginx apt repository
      apt_repository:
        repo: deb http://nginx.org/packages/ubuntu focal nginx
        state: present
</pre>

Followed by installing Nginx.
<pre class="file" data-filename="playbook.yml" data-target="append">
    - name: Install Nginx
      apt:
        name: nginx
        state: present
        update_cache: true

</pre>

Now we can run the playbook, and we should see that Nginx is installed.

`ansible-playbook -i inventory.yml --diff playbook.yml`{{execute}}

Check the version of Nginx
`nginx -v`{{execute}}

If we run `ansible-playbook` again we'll see that nothing is changed.
Ansible sees that the Nginx apt repository and package are already present, so doesn't change anything.

`ansible-playbook -i inventory.yml --diff playbook.yml`{{execute}}

Nginx is installed, but it's not running as you can see by trying `curl localhost`{{execute}}.
Start Nginx, and ensure it's automatically started at boot-up.

<pre class="file" data-filename="playbook.yml" data-target="append">
    - name: Start Nginx, ensure it automatically starts on boot
      service:
        name: nginx
        enabled: true
        state: started
</pre>

`ansible-playbook -i inventory.yml --diff playbook.yml`{{execute}}

Nginx is now running, you should be able to see the default index page at
https://[[HOST_SUBDOMAIN]]-80-[[KATACODA_HOST]].environments.katacoda.com

Now let's modify the Nginx configuration, and add a webpage.
We'll use `/srv/www/` as the HTML root directory.

Create a `default.conf`{{open}} Nginx configuration file.

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

Now go back to `playbook.yml`{{open}}.
Create the `/var/www` directory, and overwrite the default Nginx configuration with our own file so we can serve content from this directory.

<pre class="file" data-filename="playbook.yml" data-target="append">
    - name: Create www directory
      file:
        path: /srv/www
        state: directory

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
#TODO-add-more-tasks

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

Go to the webserver URL again and refresh the page (it may be cached in your browser), this time you'll see an error since there is no content in `/srv/www/`
https://[[HOST_SUBDOMAIN]]-80-[[KATACODA_HOST]].environments.katacoda.com

Finally we can add some content.
Create an `index.html`{{open}} file.

<pre class="file" data-filename="index.html" data-target="replace">
&lt;!doctype html&gt;
&lt;html lang=en&gt;
&lt;head&gt;
  &lt;meta charset=utf-8&gt;
  &lt;title&gt;Hello!&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
  &lt;h1&gt;HAnsible Rocks&lt;/h1&gt;
  &lt;figure&gt;
    &lt;img src=&quot;penguin.jpg&quot; /&gt;
    &lt;figcaption&gt;
      &lt;a href=&quot;https://commons.wikimedia.org/wiki/File:Penguin_in_Antarctica_jumping_out_of_the_water.jpg&quot;&gt;Christopher Michel&lt;/a&gt;, &lt;a href=&quot;https://creativecommons.org/licenses/by/2.0&quot;&gt;CC BY 2.0&lt;/a&gt;, via Wikimedia Commons
    &lt;/figcaption&gt;
  &lt;/figure&gt;
&lt;/body&gt;
&lt;/html&gt;
</pre>

Update `playbook.yml`{{open}} to copy this HTML file to the web root, and to download a picture of a penguin.
Add the following at the end of the `tasks` list, just before the `handlers` section

<pre class="file" data-filename="playbook.yml" data-target="insert" data-marker="#TODO-add-more-tasks">
    - name: Create index.html
      copy:
        dest: /srv/www/index.html
        src: index.html

    - name: Download an image of a penguin
      get_url:
        dest: /srv/www/penguin.jpg
        url: https://upload.wikimedia.org/wikipedia/commons/thumb/1/1d/Penguin_in_Antarctica_jumping_out_of_the_water.jpg/640px-Penguin_in_Antarctica_jumping_out_of_the_water.jpg

</pre>

Run `ansible-playbook -i inventory.yml --diff playbook.yml`{{execute}} and check that
https://[[HOST_SUBDOMAIN]]-80-[[KATACODA_HOST]].environments.katacoda.com now shows your page.
