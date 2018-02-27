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

Note, always use fail2ban-client, [never][link01] call fail2-ban-server directly.


### Configuration ###

Once installed, we want to configure Fail2ban. It keeps its configuration files in `/etc/fail2ban/`. The main configuration file is called 
`fail2ban.conf` and the 'jail' file (which has the filters for various services) is called 'jail.conf'.

Best practice is to not modify the master files but instead to create `.local` copies that override the `.conf` files:

```
sudo cp /etc/fail2ban/fail2ban.conf /etc/fail2ban/fail2ban.local
sudo vi /etc/fail2ban/fail2ban.local
```

```
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo vi /etc/fail2ban/jail.local
```

Footnotes
---------

<a name="footnote01">1</a>: The Fail2ban homepage: <https://www.fail2ban.org/wiki/index.php/Main_Page>




[link01]: https://www.fail2ban.org/wiki/index.php/Fail2Ban 'Fail2ban.org: Wiki'
