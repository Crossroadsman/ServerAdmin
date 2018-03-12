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
