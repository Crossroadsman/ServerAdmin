LAMP Stack on Ubuntu 16.04
==========================

1: Before starting
------------------

- [Perform system install][link01]
- [Do security setup][link01]
- Run software updates:  
  `sudo apt-get update && sudo apt-get upgrade`


2: Install Apache
-----------------

`sudo apt-get install apache2`


3: Configure Apache
-------------------

Set a global *ServerName* to suppress warnings. Do this by editing `/etc/apache2/apache2.conf` and add the following line:

```
ServerName <my_server_FQDN>
```

Then check for syntax errors by entering:

```
sudo apache2ctl configtest
```

The output should be `Syntax OK`.

Then restart Apache for config changes to take effect:

```
sudo systemctl restart apache2
```


4: Configure Firewall
---------------------

Specify to *UFW* the desired *Apache* application profile. First, check the list of available applications and profiles:

```
sudo ufw app list
```

You can get information about any particular profile by like follows:

```
sudo ufw app info "Apache Full"
```

(Observe that the `Ports: 80,443/TCP` text means that the profile will operate on TCP connections to ports 80 (http) and 443 (https))

Allow incoming ('in') traffic in accordance with the profile:

```
sudo ufw allow in "Apache Full"
```


5: Confirm Apache is Reachable From the Web
-------------------------------------------

In a browser enter the FQDN that we configured in `/etc/apache2/apache2.conf`. You should see the default Apache test page.


6: Install MySQL
----------------

```
sudo apt-get install mysql-server
```

7: Configure MySQL
------------------

Start by running a simple, interactive security script:

```
mysql_secure_installation
```

Option                   | Suggested Selection | Reason
-------------------------|---------------------|---------
VALIDATE PASSWORD PLUGIN | No                  | This can cause issues if there are weak passwords in use with software that automatically configures mysql user credentials
CHANGE ROOT PASSWORD     | No                  |  
<all other questions>    | Yes                 |  



[link01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/LinodeAdminChecklist.md
