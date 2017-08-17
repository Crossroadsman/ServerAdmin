Linode Administration Checklist
===============================

Provision VPS
-------------
☐ Deploy image

☐ Boot Machine

☐ Get IP address

First-time Log in
-----------------

☐ ssh in as root

☐ perform software updates

☐ create & activate hostname

- option 1:
    - `echo "my_hostname" > /etc/hostname` (create) then 
    - `hostname -F /etc/hostname` (apply the changes to the running server)
- option 2:
    - `hostnamectl set-hostname my_hostname` (create and apply in one step)

☐ edit hosts

☐ specify timezone

- `dpkg-reconfigure tzdata`


Secure Server
-------------

☐ regularly install security updates

☐ create limited user account

- `adduser my_user_name`

☐ add limited user to sudo group

- `adduser my_user_name sudo`

☐ create SSH keypair on local machine

☐ copy public key to server

☐ disallow root logins over ssh

- edit `/etc/ssh/sshd_config`

☐ restart the ssh daemon

☐ install fail2ban

☐ initialise ufw (and allow ssh)

Other Management
----------------

☐ Enable longview
