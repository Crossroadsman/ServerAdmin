Nginx
=====

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
  - verify tha the service is running  
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
  - `/etc/nginx/sites-available/`: the directory where per-site 'server blocks'<sup>[1]('#footnote01')</sup> are stored. Need to be enabled by linking to the 
    'enabled' directory
  - `/etc/nginx/sites-enabled/`: the directory where **enabled** per-site 'server blocks' are stored.
  - `/etc/nginx/snippets/`: configuration fragments that can be reused

#### Server Logs ####
- `/var/log/nginx/access.log`
- `/var/log/nginx/error.log`





A: Footnotes
------------
<a name="footnote01">1</a>: '[Server blocks](https://www.nginx.com/resources/wiki/start/topics/examples/server_blocks/)' are nginx's equivalent of Apache's virtual hosts. These allow multiple domains to be served 
by a single server. [Here](https://www.digitalocean.com/community/tutorials/how-to-set-up-nginx-server-blocks-virtual-hosts-on-ubuntu-16-04) is a Digital Ocean guide to using server blocks. 
