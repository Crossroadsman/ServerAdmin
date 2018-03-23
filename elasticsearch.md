ElasticSearch
=============

Pre-requisites
--------------

- Install the most current [JVM][link01]
- (Optional) confirm that the OS is included in the [ElasticSearch Support Matrix][link02]


VERSION1...
Installation
------------
### Configure apt for Use With Elastic's Repository ###
- Install Elastic's signing key:  
  ```
  wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
  ```
  **Notes**:
  - `-q` means [`quiet`][link03] (turn off output)
  - `-O <file>` is an alias for [`--output-document=<file>`][link04] which tells wget that instead of writing the documents to the 
    appropriate files, all the files should be concatenated together and written to the specified <file>
  - `-` used as an argument to `-O` means that stdout should be used as the 'file'
  - `apt-key` is the [APT key management utility][link05]
  - `add <file>` adds a new key to the list of trusted keys. Using `-` as the file means to get the key from stdin
- Type `apt-key list` to verify that Elastic's signing key was added to the key manager
- Install `apt-transport-https` if not installed already:  
  `sudo apt-get install apt-transport-https`
- Add Elastic's repo definition to apt's list of known sources:  
  `echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt-c`  
  **Notes**:  
  - `-a` tells tee to append to the file instead of replacing it
- Update apt's package list
  
### Install Elasticsearch ###
`sudo apt-get install elasticsearch`

Configuration
-------------
- Specify the network settings by:
  - editing the configuration file:
    `sudo vi /etc/elasticsearch/elasticsearch.yml`
  - and setting the following properties:
    ```
    network.host: "localhost"
    http.port:9200
    ```
**NOTE we may need to open port 9200 in ufw**
END VERSION1

VERSION2
Follow Elastic's [instructions][link06] for manually downloading and installing elastic search:
- Obtain the `.deb` package  
  `wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.3.deb`
- Obtain the SHA hash file  
  `wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.3.deb.sha512`
- Ensure that the hash is ok<sup>[1](#footnote01)</sup>  
  `shasum -a 512 -c elasticsearch-6.2.3.deb.sha512`
- Install the application  
  `sudo dpkg -i elasticsearch-6.2.3.deb`


Set Elasticsearch to run as a service
-------------------------------------
- Check if the system uses SysV `init` or `systemd` to manage processes:  
  `ps -p 1`  
  Most newer distributions use `systemd`, so we will assume that for the rest of these instructions. For guidance on systems that use
  SysV `init`, see Elastic's [instructions][link06]

- Restart systemd's manager configuration<sup>[2](#footnote02)</sup>:  
  `sudo /bin/systemctl daemon-reload`
- Enable Elasticsearch to start on boot:  
  `sudo /bin/systemctl enable elasticsearch.service`

Note that we can start and stop elasticsearch as follows:
```
sudo systemctl start elasticsearch.service
sudo systemctl stop elasticsearch.service
```

Note that starting/stopping the service doesn't give any feedback on whether Elasticsearch started successfully or not. To check this (and 
other troubleshooting of Elasticsearch) check the log files in `/var/log/elasticsearch`.

- Check that Elasticsearch is running:  
  `curl localhost:9200`


Footnotes
---------
<a name="footnote01">1</a>: `shasum` might not be installed. If it is not installed, it can be obtained by installing, e.g., 
perl: `sudo apt-get install perl`
<a name="footnote02">2</a>: This is a '[soft reload][link07]' that updates configurations and generators without interrupting open sockets. Here
we are running it so that it can pick up the config from the newly installed Elasticsearch


[link01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/java.md
[link02]: https://www.elastic.co/support/matrix
[link03]: https://www.gnu.org/software/wget/manual/html_node/Logging-and-Input-File-Options.html#Logging-and-Input-File-Options
[link04]: https://www.gnu.org/software/wget/manual/html_node/Download-Options.html#Download-Options
[link05]: https://www.systutorials.com/docs/linux/man/8-apt-key/
[link06]: https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html#deb-repo
[link07]: https://unix.stackexchange.com/questions/364782/what-does-systemctl-daemon-reload-do
