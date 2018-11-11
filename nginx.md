Nginx
=====

0: Background
-------------
For a system using nginx and django, the behaviour of the various components is as follows:

```
                                   [static files]
                                  /
                            <serves directly>  
                               /
[ The Internet ] <----> [nginx]
                               \
                           <WSGI protocol>
                                  \
                                    [gunicorn] <-(socket)-> [django dynamic app]
```

nginx can serve static content (media/css/etc) directly but can't control django apps, so needs an intermediary app server (typically
gunicorn).


1: Installing Nginx
-------------------
- update the apt package cache  
  `sudo apt-get update -y`  
  `sudo apt-get upgrade -y`
 
- install nginx  
  `sudo apt-get install nginx`
 
- allow nginx through the firewall
  - check that nginx registered itself as a service for ufw when it was installed:  
    `sudo ufw app list`
  - we should see three entries: `Full`, `HTTP` and `HTTPS`. `HTTP` is port 80, `HTTPS` port 443 and `Full` is both.
  - enable http by typing:  
    `sudo ufw allow 'Nginx HTTP'`
  - then verify the change:  
    `sudo ufw status`

- check nginx is running  
  - verify that the service is running (should show `active (running)`)  
    `systemctl status nginx`
  - check that nginx is visible to a web browser


2: Manage the nginx Service
---------------------------
When nginx is installed, it sets itself to load on boot.
You can use `systemctl` to override this setting:

Command                        | Result
-------------------------------|---------
`sudo systemctl stop nginx`    | stop the web server
`sudo systemctl start nginx`   | start the web server
`sudo systemctl restart nginx` | stop and restart the web server
`sudo systemctl reload nginx`  | reload configuration files (soft restart that doesn't drop any connections)
`sudo systemctl disable nginx` | prevent nginx from loading on boot
`sudo systemctl enable nginx`  | set nginx to load on boot


3: Configure nginx
------------------

### Nginx files and directories ###

#### Content ####
- `/var/www/html/`: the default directory for website content

#### Config files ####
- `/etc/nginx/`: the configuration directory
  - `/etc/nginx/nginx.conf`: the main nginx configuration file
  - `/etc/nginx/sites-available/`: the directory where per-site 'server blocks'<sup>[1]('#footnote01')</sup> are stored. Need to be 
    enabled by linking to the 'enabled' directory
  - `/etc/nginx/sites-enabled/`: the directory where **enabled** per-site 'server blocks' are stored. Typically in the form of symlinks 
    to the corresponding server block entry in the `sites-available` directory.
  - `/etc/nginx/snippets/`: configuration fragments that can be reused

#### Server Logs ####
- `/var/log/nginx/access.log`
- `/var/log/nginx/error.log`


4: Configure a Server<sup>[2]('#footnote02')</sup>
---------------------

### Example1: ###

```nginx
server {
    listen 80;
    server_name example.com;
    root /var/www/example;
    location / {
        try_files $uri $uri/ /index.html =404;
    }
}
```

### Example2: ###

```nginx
server {
    listen 80;
    server_name www2.staging.mysite.com;
    
    location /static {
        alias /home/unixuser/sites/www2.staging.mysite.com/static_root;
    }
    
    location / {
        proxy_pass http://unix:/tmp/www2.staging.mysite.com.socket;
        proxy_set_header Host $host;
}
```

Notes:
- Each server in a config file is namespaced with a `server { }` block;
- Inside the block are all the 'directives' for the server, terminated with a semicolon, such as [`listen`][link02];
- [`server_name`][link03] matches the server as specified in the http(s) request. E.g., if you requested www.mysite.com then a 
  `server_name` of `mysite.com` would match it. Similarly if you are using A records to route multiple domains to a single 
  physical server, each one can have a server block with a `server_name` corresponding to the name per the A record to host 
  multiple sites on a single nginx instance;
- [`root`][link04] is the location for static files. If we are using a Django app we can instead specify a url where all static files
  will be saved in a `location` directive (see Example2);
- [`location`][link05] is a directive with two parameters: a string or regex (here `/`) representing a uri and then a block for what
  actions nginx should take when receiving a request with uri that matches the string/regex. In this case, the the '/' is being treated
  as a regex, so any pattern that matches (e.g., `/whatever`) will trigger the enclosed block. This is why in Example2 we need to put
  `/static` before `/`;
- ['try_files'][link06] is a directive that takes a list of filenames and uses the first found file for request processing. Optionally, 
  can take a `=<code>` argument that specifies which http response code to serve if no file match is found. The path is determined by 
  reference to the `root` (and/or `alias`) directive. `$uri` is the part of the url that comes after the `root`. A filename with a 
  trailing slash represents a directory;
- `alias` is a directive that substitutes the specified path for the matched path. In Example2, if nginx receives a http request for 
  `www2.staging.mysite.com/static/styles.css` it will respond with with the file at 
  `/home/unixuser/sites/www2.staging.mysite.com/static_root/styles.css`.
- `proxy pass` will take any incoming http request and pass it over to a specified url:port (e.g., `localhost:8000`) or a specified
  socket (as in Example2). The application listening at the specified url:port or socket will handle the request, pass back the
  response to nginx, which will serve it back to the requesting client.
- By default, nginx strips the host header from requests that is proxying (so that the destination app thinks it is receiving a request 
  on localhost). This can cause problems for, e.g., Django apps which match the host header to its ALLOWED_HOSTS list.  
  `proxy_set_header` can tell nginx to reconstruct the host header (or construct it if it was missing originally).

5: Setting Up to Work With Django
---------------------------------

Collect all the static files into the directory that nginx will be serving from:
```
python manage.py collectstatic
```


6: Ensure Firewall has an Exception for HTTP (and HTTPS if Applicable)
----------------------------------------------------------------------

See: [Securing the Server][link07]


7: Set up Gunicorn
------------------
See: [Gunicorn][link08]


A: Footnotes
------------
<a name="footnote01">1</a>: '[Server blocks][link01]' are nginx's equivalent of Apache's virtual hosts. These allow multiple domains to be
served by a single server. 
[Here](https://www.digitalocean.com/community/tutorials/how-to-set-up-nginx-server-blocks-virtual-hosts-on-ubuntu-16-04) is a Digital 
Ocean guide to using server blocks.  
<a name="footnote02">2</a>: Typically we would start by unlinking default from `sites-enabled` and create a new server file in 
`sites-available` and create a symlink to it in `sites-enabled`.




[link01]: https://www.nginx.com/resources/wiki/start/topics/examples/server_blocks/
[link02]: http://nginx.org/en/docs/http/ngx_http_core_module.html#listen
[link03]: http://nginx.org/en/docs/http/ngx_http_core_module.html#server_name
[link04]: http://nginx.org/en/docs/http/ngx_http_core_module.html#root
[link05]: http://nginx.org/en/docs/http/ngx_http_core_module.html#location
[link06]: http://nginx.org/en/docs/http/ngx_http_core_module.html#try_files
[link07]: https://github.com/Crossroadsman/ServerAdmin/blob/master/SecuringServer.md
[link08]: https://github.com/Crossroadsman/ServerAdmin/blob/master/gunicorn.md
