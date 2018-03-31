Configuring a Local Server
==========================

Create an Installer
-------------------
[Create Ubuntu installer][link01]
See also <https://www.nushackers.org/2017/11/digest-ubuntu-on-macbook/>

Install Linux
-------------
[Install Ubuntu][link02]


Configure Server
----------------
### Create a User ###
Create a user (part of the install process if using Desktop)
Add the user to the sudo group (`adduser <username> sudo`)

### Configure SSH ###
Install ssh server:
```
sudo apt-get install openssh-server
```

make sure ssh is installed

sudo service ssh status

Make sure that ssh connections are possible (ssh from another machine on the local network using the local IP address)

### Configure ufw ###


### Configure external connections ###

Go to the router's (e.g., Orbi) web admin portal and set up port forwarding for ssh (so that incoming connections to the one public ip 
address get redirected to the server)

In netgear orbi go to ADVANCED / Advanced Setup / Port Forwarding Port Triggering
<https://kb.netgear.com/31069/How-do-I-configure-port-forwarding-on-my-Orbi-system>

Go to the router's web admin portal and reserve an IP address for the server.

In netgear orbi this is ADVANCED / Setup / LAN Setup, and at the bottom of the page is the section to reserve IP addresses.
<https://www.manualslib.com/manual/1237157/Netgear-Orbi.html?page=67>


[link01]: https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-macos#0
[link02]: https://www.lifewire.com/dual-boot-linux-and-mac-os-4125733
