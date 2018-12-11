Jenkins
=======

- [Intro](#s1)
- [Installation](#s2)
  1. [Install Jenkins-Compatible Java](#s2.1)
  2. [Download and Install Jenkins](#s2.2)
  3. [Download and Install Other Dependencies](#s2.3)
  4. [Add Swap](#s2.4)
  5. [Open Firewall for Jenkins](#s2.5)
  6. [Unlock Jenkins Web Panel](#s2.6)
  7. [Switch Jenkins to HTTPS](#s2.7)
     7.1. [Install nginx](#s2.7.1)
     7.2. [Create SSL Certificate](#s2.7.2)
          7.2.1. [Self-Signed Cert](#s2.7.2.1)
          7.2.2  [Let's Encrypt Cert](#s2.7.2.2)

<a name="s1"> </a>
Intro
-----
Jenkins is a popular CI server with a rich plugin ecosystem.

<a name="s2"> </a>
Installation
------------
Ideally, Jenkins should be installed on a separate server to the staging and 
production servers.

<a name="s2.1"> </a>
### 1. [Install a Jenkins-Compatible Version of Java][jenk_02] ###
There are separate run and job execution requirements for Jenkins installations.

#### 1.1. Running Jenkins ####
As of December 2018, Java 8 (32/64) is the ONLY supported runtime environment.

These requirements apply to all components of the Jenkins system including:
- Jenkins master,
- all types of agents,
- CLI clients,
- other components

#### 1.2. Executing Jobs on Jenkins ###
Jenkins jobs can be executed on Java versions different from the master/agent
runtime. Generally, Jenkins allows **any** version of JRE/JDG to be invoked
during the build, including:
- execution of Java commands from CLI,
- installation and execution of build steps based on the plug:jdk-tool plugin.

[Particular plugins may have their own Java requirements][jenk_02].

#### 1.3. [Install Java](https://github.com/Crossroadsman/ServerAdmin/blob/master/java.md) ####
You can check the current version of Java:
```console
$ java -version
openjdk version "11.0.1" 2018-10-16
OpenJDK Runtime Environment (build 11.0.1+13-Ubuntu-2ubuntu1)
OpenJDK 64-Bit Server VM (build 11.0.1+13-Ubuntu-2ubuntu1, mixed mode, sharing)
```

To install a specific version of Java (substitute a different version 
as applicable):
```console
$ sudo apt install openjdk-8-jdk
```

Then select Java 8 as default:
```console
$ sudo update-alternatives --config java
```

<a name="s2.2"> </a>
### 2. Download and Install Jenkins ###
There have been [issues][tddp_01] with the Ubuntu-distributed version of 
Jenkins (bugs re locale/unicode, etc) so download from the official Jenkins apt
repo. The process for this is to add Jenkins' GPG key to the keyring and
Jenkins' apt repo url to the apt repo list.

1. [Get the official apt key for jenkins-ci.org and add it to the local apt 
   keyring][jenk_01]:
   ```console
   $ wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key |\
   sudo apt-key add -
   OK
   ```
   **Note 1**: `-q` means 'quiet', `-O -` means redirect output to stdout  
   **Note 2**: `add -` means read the key from stdin

2. Create a jenkins [`.list`][stko_01] file in 
   `/etc/apt/sources.list.d`<sup>[1](#footnote01)</sup>:
   ```console
   $ echo deb http://pkg.jenkins.io/debian-stable binary/ |\
   sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
   ```

3. Tell `apt` to update its package list:
   ```console
   $ sudo apt update
   ```

4. Install Jenkins:
   ```console
   $ sudo apt install jenkins
   ```

<a name="s2.3"> </a>
### 3. Download Other Dependencies<sup>[2](#footnote02)</sup> ###

1. Venv and Pip for Python
   ```console
   $ apt install python3-venv python3-pip
   ```

2. Firefox and Xvfb
   ```console
   $ apt install firefox xvfb
   ```

3. Pre-requisites for building Fabric
   ```console
   $ apt install build-essential libssl-dev libffi-dev
   ```

4. [Geckodriver](https://github.com/mozilla/geckodriver/releases)
   ```console
   $ wget https://github.com/mozilla/geckodriver/releases/download/v0.23.0/geckodriver-v0.23.0-linux64.tar.gz
   Resolving github.com (github.com)... 192.30.255.112, 192.30.255.113
   ...
   2018-12-06 15:25:12 (6.01 MB/s) - ‘geckodriver-v0.23.0-linux64.tar.gz’ saved [3876109/3876109]
   $ sudo tar -xvf geckodriver-v0.23.0-linux64.tar.gz -C /usr/local/bin
   geckodriver
   $ geckodriver --version
   geckodriver 0.23.0 ( 2018-10-04)
   ...
   ```

<a name="s2.4"> </a>
### 4. Add Some Swap ###
You can check the current swap status by looking at swap allocations:
```console
$ sudo swapon --show
/dev/sdb partition 512M 268K   -2
```

and memory usage:
```console
$ sudo free -h
              total        used        free      shared  buff/cache   available
Mem:          985Mi       327Mi       199Mi       5.0Mi       458Mi       512Mi
Swap:         511Mi       0.0Ki       511Mi
```
**Note**: `-h` is for human-readable

Add some additional swap as follows:
```console
$ sudo fallocate -l 4G /swapfile
$ sudo mkswap /swapfile
mkswap: /swapfile: insecure permissions 0644, 0600 suggested.
Setting up swapspace version 1, size = 4 GiB (4294963200 bytes)
no label, UUID=d18b6b4b-6440-4c3b-93ef-a1358427e99f
$ sudo chmod 600 /swapfile
$ sudo swapon /swapfile
```
**Note**: Adding additional swap to VPSs with SSD storage is not recommended.

- Make the new swap persist across reboots by adding the new swap 
  to `/etc/fstab` (append the following line):
  ```
  /swapfile  none  swap  sw  0  0
  ```

For more on swap, see [Digital Ocean: How to Add Swap Space][digo_01].

<a name="s2.5"> </a>
### 5. Open [Firewall][serv_01] for Jenkins' Ports ###

```console
$ sudo ufw allow 8080 comment 'jenkins'
Rule added
Rule added (v6)
```

<a name="s2.6"> </a>
### 6. Unlock Jenkins and Initial Web Setup ###
- In a web browser go to the server's host IP, port 8080 and review the Jenkins 
  unlock instructions.

- Once unlocked, choose 'suggested' plugins, then wait as they are installed.

- Create an admin username and password.

- Once in the main dashboard:
  - select `Manage Jenkins` from the left sidebar,
  - then select `Manage Plugins`, 
  - `Available`

- Use the search to find, then install, the following plugins:
  - `ShiningPanda` (for Python support);
  - `Xvfb` (for headless web browsing)

- Reboot the server (`sudo reboot`).

- Tell Jenkins where to find Python 3:
   - Go to `Manage Jenkins`,
   - `Global Tool Configuration`,
   - `Python` -> `Python installations` -> `Add Python`,
   - give it a name (no whitespace), e.g., `System-Python3`,
   - fill in the path (you can use `which python3` on the server to find it),
     e.g., `/usr/bin/python3`

- Tell Jenkins where to find Xvfb:
   - Go to `Manage Jenkins`,
   - `Global Tool Configuration`,
   - `Xvfb installation` -> `Add Xvfb`  
     (if you can't see `Xvfb installation`, make sure you rebooted the server
     after installing the Xvfb plugin),
   - enter `/usr/bin` as the installation directory.

<a name="s2.7"> </a>
### 7. Switch Jenkins to HTTPS ###
We'll use Nginx to redirect https traffic (port 443) to Jenkins (port 8080).

Note that the Nginx config needs to be different depending on whether the 
Jenkins URL is a subdomain (e.g., `jenkins.domain.com`), a folder off 
another URL (e.g., the tested application is at `https://www.domain.com` 
and Jenkins is at `https://www.domain.com/jenkins`).

<a name="s2.7.1"> </a>
#### 7.1. Install Nginx (Steps 1 and 2 of [this guide][serv_02]) ####

<a name="s2.7.2"> </a>
#### 7.2. Create a SSL Certificate ####
Either create a [self-signed cert](#s2.7.2.1) or, better, get a [Let's 
Encrypt cert](#s2.7.2.2).

<a name="s2.7.2.1"> </a>
##### 7.2.1. Self-Signed Cert #####
See also [Digital Ocean: How To Create an SSL Certificate on Nginx for 
Ubuntu 14.04][digo_02].

Note that a self-signed cert is only a temporary solution before the server goes
into production as most browsers will vehemently mistrust self-signed certs.

- Make a directory to hold SSL information (inside the Nginx config directory):
  ```console
  $ sudo mkdir /etc/nginx/ssl
  ```

- Use OpenSSL to create the cert:
  ```console
  $ sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt
  ```

The arguments to openssl are:
- `req -x509` : This is the subcommand to tell openssl that we want X.509
  certificate signing request (CSR) management (for SSL/TLS compliance). The
  `-x509` argument to the subcommand is what specifies we are self-signing
  the cert rather than generating a certificate signing request;
- `nodes` : Don't create a passphrase (we need the cert to be usable by
  nginx without user intervention);
- `-days 365` : The certificate expiration period;
- `-newkey rsa:2048` : Create the key along with the certificate (as opposed
  to providing openSSL a ready-made key to associate with the certificate);
- `-keyout /etc/nginx/ssl/nginx.key` : the file to write the key to;
- `-out /etc/nginx/ssl/nginx.crt` : the file to write the cert to.

- OpenSSL will prompt you with a few questions. Most of the answers are 
  obvious, but pay attention to:  
  ```
  Common Name (e.g. server FQDN or YOUR name) []:
  ```

  OpenSSL suggests that it should be a FQDN (e.g., 'subdomain.domain.com')
  but the Digital Ocean [guide][digo_02] suggests a bare domain (e.g., 
  'domain.com').

<a name="s2.7.2.2"> </a>
##### 7.2.2. Use Let's Encrypt to Create a Cert #####
See also: [Digital Ocean: How To Secure Nginx with Let's Encrypt on 
Ubuntu 18.04][digo_03] or [Eff: Nginx on Ubuntu 18.04 LTS (bionic)][cbot_01].

- Install [Certbot][cbot_01] (Eff's tool for generating SSL certs from
  Let's Encrypt):
  - update apt's package list:
    ```console
    $ sudo apt update
    Hit:1 http://mirrors.linode.com/ubuntu cosmic InRelease
    ...
    1 package can be upgraded. Run 'apt list --upgradable' to see it.
    ```
  - install software-properties-common, a bunch of scripts useful for adding/
    removing PPAs. Most significantly these scripts mean you don't need to
    manually edit `/etc/apt/sources.list` or `/etc/apt/sources.list.d` when 
    adding/removing PPAs:
    ```console
    $ sudo apt install software-properties-common
    Reading packag lists... Done
    ...
    software-properties-common is already the newest version (0.96.27).
    0 upgraded, 0 newly installed, 0 to remove and 1 not upgraded.
    ```
  - add Universe to the repository list (equivalent to adding the PPA to
    `/etc/apt/sources.list` and dowloading the associated GPG key and adding
    to the apt keyring). 'Universe' is one of the four main Ubuntu-provided
    offical repos (Main, Restricted, Universe, Multiverse). Universe receives
    less support from Canonical than Main and Restricted, so it is not always
    enabled by default:
    ```console
    $ sudo add-apt-repository universe
    'universe' distribution component enabled for all sources.
    ...
    Fetched 22.9 MB in 7s (3,429 kB/s)
    Reading package lists... Done
    ```
  - add the Certbot PPA to the repository list (You might get an error if
    the PPA does not have a release for the corresponding Ubuntu version, 
    in this case 18.10):
    ```console
    $ sudo add-apt-repository ppa:certbot/certbot
    This is the PPA for packages prepared by Debian Let's Encrypt Team and
    backported for Ubuntu(s).
    ...
    Press [ENTER] to continue or Ctrl-c to cancel adding it
    Hit:1 http://mirrors.linode.com/ubuntu cosmic InRelease
    ...
    Err:10 http://ppa.launchpad.net/certbot/certbot/ubuntu cosmic Release
      404  Not Found [IP: 91.189.95.83 80]
    Reading package lists... Done
    E: The repository 'http://ppa.launchpad.net/certbot/certbot/ubuntu cosmic
    Release' does not have a Release file.
    N: Updating from such a repository can't be done securely, and is therefore
    disabled by default.
    N: See apt-secure(8) manpage for repository creation and user configuration
    details.
    ```
  - re-run apt update to get add to the package list any packages from the 
    newly-added repos
    ```console
    $ sudo apt update
    ```
  - install certbot (in this case, the nginx-specific version). Note, if there
    is no current version available from the PPA (see the error above), there
    might be a version available from the main Ubuntu repos (as in this case):
    ```console
    $ sudo apt install python-certbot-nginx
    Reading package lists... Done
    ...
    The following additional packages will be installed:
      certbot python-pyicu python3-acme python3 certbot python3-certbot-nginx
      ...
    0 upgraded, 19 newly installed, 0 to remove and 1 not upgraded.
    Need to get 1,224 kB of archives.
    After this operation, 6,533 kB of additional disk space will be used.
    Do you want to continue? [Y/n] y
    ...
    Setting up python3-certbot-nginx (0.26.0-1) ...
    Setting up python-certbot-nginx (0.26.0-1) ...
    ```

- At this point, certbot is installed and ready to use, but we need to 
  configure SSL for Nginx:
  - ensure that the server block has a `server_name` line that matches
    the certificate we will be getting. The whole config file can look
    like the one in [7.3 Create a Server Config File](#s7.3).

- The certbot website [mentions][cbot_01] that DNS plugins are also available 
  which can be used to get a wildcard certificate. This is worth looking into 
  but we won't be using this here.

- Get the certificate using certbot:
  ```console
  $ sudo certbot --nginx certonly
  Saving debug log to /var/log/letsencrypt/letsencrypt.log
  Plugins selected: Authenticator nginx, Installer nginx
  Enter email address (used for urgent renewal and security notices)(Enter 'c'
  to cancel):
  ```
- Type in the email address that should receive renewal and security notices:
  ```
  security@my_domain.com
  ```
- Agree to the ToS
- Decide whether to share email with EFF
- Select the names from the list that you wish to activate HTTPS:
  ```
  Which names would you like to activate HTTPS for?
  - - - - - - - - - - - - - - - - - - - - - - - - - - -
  1: jenkins.my_domain.com
  - - - - - - - - - - - - - - - - - - - - - - - - - - -
  Select the appropriate numbers separated by commas and/or spaces, or leave
  input blank to select all options shown (Enter 'c' to cancel):
  1
  ```
- Wait while certbot does its thing:
  ```
  Obtaining a new certificate
  Performing the following challenges:
  http-01 challenge for jenkins.my_domain.com
  Waiting for verification...
  Cleaning up challanges
  ```
- Take note of where certbot saved the cert/chain and key:
  ```
  IMPORTANT NOTES:
   - Congratulations! Your certificate and chain have been saved at:
     /etc/letsencrypt/live/jenkins.my_domain.com/fullchain.pem
     Your key file has been saved at:
     /etc/letsencrypt/live/jenkins.my_domain.com/privkey.pem
  ```
- Also note that Let's Encrypt certs expire after 90 days, so it's worth
  checking that the automated renewal cron job (installed with the certbot
  packages) is working:
  ```console
  $ sudo certbot renew --dry-run
  Saving debug log to /var/log/letsencrypt/letsencrypt.log
  ...
  Cert not due for renewal, but simulating renewal for dry run
  ...
  ** DRY RUN: simulating 'certbot renew' close to cert expiry
  **          (the test certificates below have not been saved.)
  
  Congratulations, all renewals succeeded. The following certs have been
  renewed:
    /etc/letsencrypt/live/jenkins.my_domain.com/fullchain.pem (success)
  ...
  ```

- Point nginx at the key and cert by editing the server config in
  `sites-available` (note we can either reference the file directly in
  the server config, or create a symlink in, e.g., `/etc/nginx/ssl` and
  put the symlink in the config file):
  ```nginx
  ...
  server_name jenkins.my_domain.com;
  
  ssl_certificate /etc/letsencrypt/live/jenkins.my_domain.com/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/jenkins.my_domain.com/privkey.pem;
  ```

- Restart nginx:
  ```console
  $ sudo systemctl restart nginx
  ```


#### <a name="s7.3">7.3</a> Create a Server Config File ####
- In `/etc/nginx/sites-available` create a server file with the public-facing
  url as a filename, e.g., if the server will be 
  at `jenkins.my_domain.com`:
  ```console
  $ sudo vi /etc/nginx/sites-available/jenkins.my_domain.com
  ```
  
See also these example use cases:
- [Example 1: Jenkins Behind Nginx in Reverse Proxy](https://wiki.jenkins.io/display/JENKINS/Jenkins+behind+an+NGinX+reverse+proxy)
- [Example 2: Running Jenkins Behind Nginx](https://wiki.jenkins.io/display/JENKINS/Running+Jenkins+behind+Nginx)

- Create a server block that looks like the following:  
  ```nginx
  upstream jenkins {
    # this is a list of servers that can the request.
    # (this isn't especially helpful while we only have one server, but
    # if we scale up then we can just add additional server IPs here without
    # changing any of the server{} block below.

    keepalive 32;  # keepalive connections
    server 127.0.0.1:8080;  # the IP and port for Jenkins
  }

  server {
    # redirect any http inbound traffic (port 80) to https

    listen 80;
    server_name jenkins.my_domain.com;
    return 301 https://$host$request_uri;
  }

  server {
    # handle any https traffic

    listen 443 ssl;

    server_name jenkins.my_domain.com;

    ssl_certificate /etc/nginx/ssl/nginx.crt;
    ssl_certificate_key /etc/nginx/ssl/nginx.key;

    # location is a regex, so `/` will match every URI
    location / {
      #  with regard to headers, nginx by default performs the following
      #  transformations on client request headers before passing to the
      #  proxied server:
      #  - strip all empty headers (there's no point sending empty values
      #    to another server, it would only bloat the request);
      #  - strip any invalid headers (this includes any headers with
      #    underscores, which can be overridden with the
      #    `underscores_in_headers on` directive);
      #  - rewrites `Host` to the value specified by `$proxy_host`
      #  - rewrites `Connection` to `close`. Thus the upstream server
      #    should not expect the connection to be persistent.
      #
      # these are the commands we use to manually manipulate the headers:
      # `proxy_set_header field value` : assigns an HTTP request header
      #     to the proxied request with the specified `field` and `value`.
      #     This will create a header if it didn't already exist or replace
      #     one that did exist
      # `proxy_redirect redirect replacement` : rewrites the `Location` and
      #     `Refresh` HTTP response header fields coming back from the
      #     proxied server before giving back to the client.
      #     Example: suppose the server sent back a response with a header:
      #     "Location: http://localhost:8000/some/uri", we could use:
      #     `proxy_redirect http://localhost:8000/some http://domain.com/other`
      #     and then the response header would be rewritten by Nginx to look
      #     like:
      #     "Location: http://domain.com/other/uri"
      #
      # variables
      # ---------
      # (see http://nginx.org/en/docs/varindex.html)
      #
      # $host : in this order of precedence: the host name from the request
      #     line, host name from the "Host" request header field, or server
      #     name matching the request.
      # $server_port : the port of the server that accepted a request.
      # $remote_addr : the client address
      # $proxy_add_x_forward_for : the client's "X-Forwarded-For" request
      #     header value with the `$remote_addr` variable appended to it,
      #     separated with a comma.
      # $scheme : the request scheme (i.e., 'http' or 'https')
      proxy_set_header Host $host:$server_port;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;

      proxy_redirect http:// https://;

      # `proxy_pass url` : Set the protocol, address (and optionally, URI) of
      # a proxied server. I.e., this is the server that the request, matching
      # the location regex (here '/') should be proxied to. It can be an
      # address literal, e.g., `http://127.0.0.1:8080` or the name of an
      # upstream (as in this case), which tells Nginx to look at the relevant
      # upstream directive to see where to send the request.
      proxy_pass http://jenkins;

      # Other stuff
      # (maybe required for Jenkins HTTP-based CLI, or for working around
      # Jenkins bugs)
      proxy_http_version 1.1;
      proxy_request_buffering off;
      proxy_buffering off;
      add_header 'X-SSH-Endpoint' 'jake.koumparossoftware.com:50022' always;
    }
  }
  ```

- Create a symlink in `sites-enabled` that points to `sites-available`:
  ```console
  $ cd ../sites-enabled
  $ sudo ln -s ../sites-available/jenkins.my_domain.com ./jenkins.my_domain.com
  ```

- Restart Nginx:
  ```console
  $ sudo systemctl restart nginx
  ```


Footnotes
---------
<a name="footnote01">1</a>: Here we're piping the string 
"deb http://pkg.jenkins.io/debian-stable binary/" into `tee` so that we can use 
sudo to give us write privileges into /etc/apt/sources.list.d (we can't just 
start the echo command with sudo and use `>` to redirect to the destination 
file because it is the redirect that needs privilege escalation, and we can't 
use sudo to elevate a redirect). Then we use `> /dev/null` to explicitly throw 
away the output from `tee` that goes to stdout.  
<a name="footnote01">2</a>: The following packages could all be installed with 
a single call to `apt install` but have been separated by context.




[cbot_01]: https://certbot.eff.org/lets-encrypt/ubuntubionic-nginx
[digo_01]: https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-16-04
[digo_02]: https://www.digitalocean.com/community/tutorials/how-to-create-an-ssl-certificate-on-nginx-for-ubuntu-14-04
[digo_03]: https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-18-04
[jenk_01]: https://jenkins.io/doc/book/installing/#debianubuntu
[jenk_02]: https://jenkins.io/doc/administration/requirements/java/#java-requirements
[serv_01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/SecuringServer.md#ufw-uncomplicated-firewall
[serv_02]: https://github.com/Crossroadsman/ServerAdmin/blob/master/nginx.md
[stko_01]: https://stackoverflow.com/a/26021071
[tddp_01]: https://www.obeythetestinggoat.com/book/chapter_CI.html#_installing_jenkins
