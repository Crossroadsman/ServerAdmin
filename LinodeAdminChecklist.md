Linode Administration Checklist
===============================


Provision VPS
-------------
☐ Go to Linode manager

☐ Shut down the existing instance

☐ Go to Rebuild and configure the settings for the new VPS

☐ Boot Machine

☐ Note the IP address

☐ Configure DNS

- [Set a low TTL][link05]
- Set the A Record

Remove old VPS from local machine's known_hosts
-----------------------------------------------

Edit `~/.ssh/known_hosts`


First-time Log in
-----------------

☐ ssh in as root

☐ perform software updates (`apt-get update -y && apt-get upgrade -y`)<sup>[1](#footnote01)</sup>
  (note: when prompted to update Grub, select '*keep the local version installed*')

☐ consider whether appropriate to perform a dist upgrade (`apt-get dist-upgrade`)

☐ create & activate hostname

- option 1:
    - `echo "my_hostname" > /etc/hostname` (create) then 
    - `hostname -F /etc/hostname` (apply the changes to the running server)
- option 2:
    - `hostnamectl set-hostname my_hostname` (create and apply in one step)

- verify the hostname has been updated by entering `hostname`

☐ configure hosts

- edit `/etc/hosts` and add:
  ```<ip address of server (from Linode Manager)> <FQDN> <hostname>```

- verify by entering `hostname -f` which should respond with the FQDN, obtained by looking up the hostname in the hosts file 
  and returning the associated FQDN

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

- `systemctl restart ssh`

☐ [install fail2ban][link04]

☐ [initialise ufw (and allow ssh)][link04]

Other Management
----------------

☐ [Configure git][link01]

☐ [Clone .vimrc][link02]<sup>[2](#footnote02)</sup>

☐ Enable longview








Footnotes
---------
<a name="footnote01">1.</a> `-y` will run non-interactively, assuming and applying 'yes' as the answer to all prompts (and aborting if
any 'undesirable' situations arise). Per the man page.  
<a name="footnote02">2.</a> Note that on the Mac, Terminal needs to be [configured][link03] to show Solarized colours



[link01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/git.md 'Crossroadsman: Server Admin: Git'
[link02]: https://github.com/Crossroadsman/.vim 'Crossroadsman: .vimrc'
[link03]: ethanschoonover.com/solarized
[link04]: https://github.com/Crossroadsman/ServerAdmin/blob/master/SecuringServer.md 'Crossroadsman: Server Admin: Securing a Server'
[link05]: https://www.dnswatch.info/articles/dns-update
