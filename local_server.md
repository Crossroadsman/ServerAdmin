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

Go to the router's (e.g., Orbi) web admin portal and set up port forwarding (so that incoming connections to the one public ip 
address get redirected to the server) for ssh and any other required services (e.g., elastic 9200, kibana 5601, etc).

In netgear orbi go to ADVANCED / Advanced Setup / Port Forwarding Port Triggering
<https://kb.netgear.com/31069/How-do-I-configure-port-forwarding-on-my-Orbi-system>

Go to the router's web admin portal and reserve an IP address for the server.

In netgear orbi this is ADVANCED / Setup / LAN Setup, and at the bottom of the page is the section to reserve IP addresses.
<https://www.manualslib.com/manual/1237157/Netgear-Orbi.html?page=67>




Getting IP Address Updates
--------------------------
Sometimes your local machine's public IP address will get changed.

To find out what your current public IP address is from command line (Linux or Mac):

Either use [dig](https://linux.die.net/man/1/dig):
- Example1: opendns.com:
  ```console
  $ dig +short myip.opendns.com @resolver1.opendns.com
  74.86.144.194
  ```
- Example2: google:
  ```console
  $ dig TXT +short o-o.myaddr.l.google.com @ns1.google.com
  "74.86.144.194"
  ```
  
Or use [host](https://linux.die.net/man/1/host):
```console
$ host myip.opendns.com resolver1.opendns.com
Using domain server:
Name: resolver1.opendns.com
Address: 208.67.222.222#53
Aliases:

myip.opendns.com has address 74.86.144.194
Host myip.opendns.com not found: 3(NXDOMAIN)
Host myip.opendns.com not found: 3(NXDOMAIN)
```

See also: [How To Find My Public IP Address From Command Line On a Linux](https://www.cyberciti.biz/faq/how-to-find-my-public-ip-address-from-command-line-on-a-linux/)


[link01]: https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-macos#0
[link02]: https://www.lifewire.com/dual-boot-linux-and-mac-os-4125733
