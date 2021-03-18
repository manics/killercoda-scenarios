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
  &lt;h1&gt;⭐⭐⭐ Ansible Rocks ⭐⭐⭐&lt;/h1&gt;
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
