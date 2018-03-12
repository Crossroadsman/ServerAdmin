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
