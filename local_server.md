Configuring a Local Server
==========================

Install Ubuntu

Create a user
Add the user to the sudo group

sudo apt-get install openssh-server

make sure ssh is installed

sudo service ssh status

Make sure that ssh connections are possible (ssh from another machine on the local network using the local IP address)

Configure external connections:

Go to the router's (e.g., Orbi) web admin portal and set up port forwarding for ssh (so that incoming connections to the one public ip 
address get redirected to the server)

In netgear orbi go to ADVANCED / Advanced Setup / Port Forwarding Port Triggering
<https://kb.netgear.com/31069/How-do-I-configure-port-forwarding-on-my-Orbi-system>

Go to the router's web admin portal and reserve an IP address for the server.

In netgear orbi this is ADVANCED / Setup / LAN Setup, and at the bottom of the page is the section to reserve IP addresses.
<https://www.manualslib.com/manual/1237157/Netgear-Orbi.html?page=67>
