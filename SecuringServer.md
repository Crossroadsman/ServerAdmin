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



Footnotes
---------

<a name="footnote01">1</a>: The Fail2ban homepage: <https://www.fail2ban.org/wiki/index.php/Main_Page>




[link01]: https://www.fail2ban.org/wiki/index.php/Fail2Ban 'Fail2ban.org: Wiki'
[link02]: https://www.tutorialspoint.com/unix/unix-system-logging.htm
