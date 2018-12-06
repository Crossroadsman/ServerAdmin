Jenkins
=======

Intro
-----
Jenkins is a popular CI server with a rich plugin ecosystem.

Installation
------------
Ideally, Jenkins should be installed on a separate server to the staging and 
production servers.

### Install a Jenkins-Compatible Version of Java ###
TODO

### Download Jenkins ###
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

### Download Other Dependencies<sup>[2](#footnote02)</sup> ###

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
   ```
   
   ```console
   $ sudo tar -xvf geckodriver-v0.23.0-linux64.tar.gz -C /usr/local/bin
   geckodriver
   ```

   ```console
   $ geckodriver --version
   geckodriver 0.23.0 ( 2018-10-04)
   ...
   ```

### Add Some Swap ###

```console
$ sudo fallocate -l 4G /swapfile
$ sudo mkswap /swapfile
mkswap: /swapfile: insecure permissions 0644, 0600 suggested.
Setting up swapspace version 1, size = 4 GiB (4294963200 bytes)
no label, UUID=d18b6b4b-6440-4c3b-93ef-a1358427e99f
$ sudo chmod 600 /swapfile
$ sudo swapon /swapfile
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




[jenk_01]: https://jenkins.io/doc/book/installing/#debianubuntu
[stko_01]: https://stackoverflow.com/a/26021071
[tddp_01]: https://www.obeythetestinggoat.com/book/chapter_CI.html#_installing_jenkins
