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

☐ perform software updates (`apt update && apt upgrade -y`)<sup>[1](#footnote01)</sup>
  (note: when prompted to update Grub, select '*keep the local version installed*')

☐ consider whether appropriate to perform a full (dist) upgrade (`apt full-upgrade`) (previously `apt-get dist-upgrade`)<sup>[2](#footnote02)</sup>.
- you can check whether there are packages available using `apt list --upgradable`

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

☐ specify timezone (use None of the Above/UTC)<sup>[2](#footnote02)</sup>

- `dpkg-reconfigure tzdata`


Secure Server
-------------

☐ regularly install security updates

☐ create limited user account

- `adduser my_user_name`<sup>[3](#footnote03)</sup>

☐ add limited user to sudo group

- `adduser my_user_name sudo`

☐ log out and log back in as limited user

☐ create SSH keypair on local machine

- `ssh-keygen -t rsa -b 4096 -C "<user@email.com>"`<sup>[4](#footnote04)</sup>

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

☐ [Clone .vimrc][link02]<sup>[5](#footnote05)</sup>

☐ Enable longview








Footnotes
---------
1. <a id="footnote01"> </a>`-y` will run non-interactively, assuming and applying 'yes' as the answer to all prompts (and aborting if
   any 'undesirable' situations arise). Per the man page.  Replaces `apt-get update && apt-get upgrade -y`. 
2. <a id="footnote02"> </a>'upgrade' is the safest option and behaves slightly different under `apt-get` than `apt`:
   i. [`apt-get upgrade`][ubun01]: will upgrade any packages that do not require installing new packages or removing 
      old packages.
   ii. [`apt upgrade`][ubun02]: will upgrade any packages. Will install new packages if required for an upgrade. Will never
      remove an old package.
   iii. [`apt full-upgrade`][ubun02] (aka [`apt-get dist-upgrade`][ubun01]: will upgrade any packages. Will install new
      packages and remove old packages as required (using smart conflict resolution where necessary).
3. <a id="footnote03"> </a>[Always use UTC](http://yellerapp.com/posts/2015-01-12-the-worst-server-setup-you-can-make.html)
4. <a id="footnote04"> </a>`adduser` is a debian-specific tool that, where available, is generally preferable to `useradd`. `adduser`
   defaults to providing a full profile while `useradd` needs you to manually specify several values. See the [TDD guide][link06] for an 
   example.  
5. <a id="footnote05"> </a>The switches (`-t rsa` type RSA, `b 4096` bitsize 4096, `-C "<email>"` comment: email address) come from 
   Github's [SSH key generation guide][link07]  
6. <a id="footnote06"> </a>Note that on the Mac, Terminal needs to be [configured][link03] to show Solarized colours  



[link01]: https://github.com/Crossroadsman/git-notes/blob/master/git.md 'Crossroadsman: Git: Git Notes'
[link02]: https://github.com/Crossroadsman/.vim 'Crossroadsman: .vimrc'
[link03]: https://ethanschoonover.com/solarized/
[link04]: https://github.com/Crossroadsman/ServerAdmin/blob/master/SecuringServer.md 'Crossroadsman: Server Admin: Securing a Server'
[link05]: https://www.dnswatch.info/articles/dns-update
[link06]: https://github.com/hjwp/Book-TDD-Web-Dev-Python/blob/master/server-quickstart.md
[link07]: https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#platform-linux
[ubun01]: http://manpages.ubuntu.com/manpages/xenial/man8/apt-get.8.html
[ubun02]: 
