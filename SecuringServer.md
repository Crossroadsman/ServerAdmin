Securing a Server
=================

Fail2Ban
--------

### What is Fail2Ban? ###

Fail2ban scans log files (which contain interesting information, especially about failed logins) and bans IP addresses that show 
malicious signs (e.g., multiple password failures, exploit-seeking behaviour, etc.). 

It is then used (typically) to update firewall rules to reject those IP addresses (and email the system 
administrator)<sup>[1]('#footnote01')</sup>.


### Installation ###

```
sudo apt-get install fail2ban
```

Once installed, test that it was installed by typing:

```
fail2ban-client -h
```

Note, always use `fail2ban-client`, [never][link01] call `fail2-ban-server` directly.


### Configuration ###

Once installed, we want to configure Fail2ban. It keeps its configuration files in `/etc/fail2ban/`. The main configuration file 
is called `fail2ban.conf` and the 'jail' file (which has the filters and actions for various services) is called 'jail.conf'.

Best practice is to not modify the master files but instead to create `.local` copies that override the `.conf` files:


#### Configuring `fail2ban.local` ####

`fail2ban.conf` is the main configuration file for Fail2ban and includes things like the [log level][link02]. This file has sensible
defaults so generally does not require much customisation.

```
sudo cp /etc/fail2ban/fail2ban.conf /etc/fail2ban/fail2ban.local
sudo vi /etc/fail2ban/fail2ban.local
```

#### Configuring `jail.local` ####

```
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo vi /etc/fail2ban/jail.local
```

The jail file contains the various filters (regex patterns to find in logfiles) and actions (behaviours in response to a filter hit) for
the listed services. Note that most common services are included by default. Following is an example for `sshd`:

```
[sshd]
enabled = true
#filter = sshd-aggressive
port    = ssh
logpath = %(sshd_log)s
backend = %(sshd_backend)s
```

Notes:
1. the name of the template is in brackets. There is a default setting for `filter` of `filter = %(__name__)s` which means that if
   no filter is specified, Fail2ban will look for file called 'sshd' (either .conf or .local) in the filter.d subdirectory.
2. here, `filter` is commented out, which means that Fail2ban will fall back to the default filter file: `sshd.conf` or `sshd.local`.
3. port is the port that Fail2ban should be referencing for this service. If using ssh's default port, you can just put `ssh` but if
   you have customised ssh to use a different port, say 3456, then you would replace `ssh` with `3456`.
4. no actions have specified, which means that only the default actions will be taken.


There are also some global default behaviours specified in this file, such as bantime and the settings for email.


#### Launch the Fail2ban Client ####

```
sudo fail2ban-client start
```


ufw (Uncomplicated FireWall)
----------------------------

### What is ufw? ###

UFW is Ubuntu's frontend for `iptables`, which in turn is a suite of commands for managing `netfilter`—Linux's standard packet filtering
system.

### Launch ufw ###

```
sudo ufw allow ssh 'allows ssh'
sudo ufw logging on
sudo ufw enable
sudo ufw status
```

Notes:
1. `allow ssh 'allows ssh'`: By default, ufw disallows everything. Here we are explicitly allowing ssh. Like with Fail2ban, if we are
   using the default port for ssh, we can refer to `ssh` otherwise we would refer to the port. E.g., if ssh was running on port 2020 the
   command would be: `sudo ufw allow 2020`. The part in quotes is a comment, which can be viewed when using `ufw status`;
2. `logging on`: This enables logging for the firewall;
3. `enable`: This actually activates the firewall;
4. `status`: This gives a short summary of the firewall's status (what ports have been specified, what actions are permitted, and which
   hosts can connect). You can also run `sudo ufw status verbose` which adds some global-level status information.

Other useful examples:

Command                                        | Explanation
-----------------------------------------------|------------
`sudo ufw allow 80/tcp`                        | Allow tcp traffic on port 80 (http)
`sudo ufw allow 443/tcp`                       | Allow tcp traffic on port 443 (https)
`sudo ufw allow 25`                            | Allow port 25 (smtpd/email)
`sudo ufw allow 3000:5000/udp`                 | Allow UDP traffic on all ports in the range 3000 to 5000
`sudo ufw allow from 71.76.215.218`            | Allow all traffic on all ports from the specified IP address
`sudo ufw allow from 1.2.3.4 to any port 5432` | allow host `1.2.3.4` to connect to any destination IP address on port 5432


#### Deleting Rules ####

Either delete by a rule's description, e.g.:
```
sudo ufw delete allow 443
```
Will delete a rule allowing access on port 443

Or get a list of rule numbers and delete rules by reference to the number:
```
sudo ufw status numbered
sudo ufw delete 2
```


Footnotes
---------

<a name="footnote01">1</a>: The Fail2ban homepage: <https://www.fail2ban.org/wiki/index.php/Main_Page>




[link01]: https://www.fail2ban.org/wiki/index.php/Fail2Ban 'Fail2ban.org: Wiki'
[link02]: https://www.tutorialspoint.com/unix/unix-system-logging.htm
