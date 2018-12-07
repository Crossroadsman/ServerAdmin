Jenkins
=======

Intro
-----
Jenkins is a popular CI server with a rich plugin ecosystem.

Installation
------------
Ideally, Jenkins should be installed on a separate server to the staging and 
production servers.

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

### 2. Download Jenkins ###
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
[jenk_02]: https://jenkins.io/doc/administration/requirements/java/#java-requirements
[stko_01]: https://stackoverflow.com/a/26021071
[tddp_01]: https://www.obeythetestinggoat.com/book/chapter_CI.html#_installing_jenkins
