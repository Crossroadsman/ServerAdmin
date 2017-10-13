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


8: Install PHP
--------------

```
sudo apt-get install php libapache2-mod-php php-mcrypt php-mysql
```


9: Configure PHP
----------------

First, tell Apache to prefer `index.php` to `index.html` by editing `/etc/apache2/mods-enabled/dir.conf` to change the line commencing `DirectoryIndex index.html ...` such that `index.php` appears before `index.html`.

Then restart Apache:

```
sudo systemctl restart apache2
```

We can check the status of Apache as follows:

```
sudo systemctl status apache2
```


10: (Optional) Install Additional PHP Modules
---------------------------------------------

```
apt-cache search php- | less
```

(Note, it's not obvious how the search term operates: `php-` returns hits for, *inter alia*, `libnet-libidn-perl`, `php7.0-cli`, `bandwidthd-psql`)

If we see a package that might be interesting we can get more information using:

```
apt-cache show <package_name>
```

And then install any desired optional modules as a space-separated list into `apt-get`:

```
sudo apt-get install <package1> <package2> <package3> ...
```


11: Test PHP Processing on the Web Server
-----------------------------------------

Create a very simple php script *info.php* and save it in the '*web root*' (which, in Ubuntu 16.04, is */var/www/html/*):

```php
<?php
phpinfo();
?>
```

Then try loading the script from a web browser by pointing it at *http://<server_FQDN>/info.php*.

Once you have successfully tested that the script loads, delete it (since it displays information that might be useful to an attacker):

```
sudo rm /var/www/html/info.php
```




[link01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/LinodeAdminChecklist.md
