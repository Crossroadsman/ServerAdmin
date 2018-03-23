ElasticSearch
=============

Pre-requisites
--------------
- Install the most current [JVM][link01]
- (Optional) confirm that the OS is included in the [ElasticSearch Support Matrix][link02]


Obtain Elasticsearch
--------------------
Follow Elastic's [instructions][link06] for manually downloading and installing elastic search:
- Obtain the `.deb` package  
  `wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.3.deb`
- Obtain the SHA hash file  
  `wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.3.deb.sha512`


Install Elasticsearch
---------------------
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


Configure Elasticsearch
-----------------------
By default, `/etc/elasticsearch` is where runtime configuration is stored. The main config file is `/etc/elasticsearch/elasticsearch.yml`.
For Debian-based systems, there is also a system config file in `/etc/default/elasticsearch` for setting things like custom path to Java
runtime, etc.

Elasticsearch has three configuration files: 
- `elasticsearch.yml` for configuring Elasticsearch;
- `jvm.options` for configuring the JVM settings;
- `log4j2.properties` for configuring logging.

The configuration files are in [YAML][link08].

Note that Elasticsearch ships with sensible defaults and a single cluster with a single node should work out of the box for demo or 
testing use without changing any settings.

For production, the following items need to be configured:

- Paths
- Cluster and Node name
- Network host
- Discovery
- Heap (size and dump path)
- GC Logging

#### Paths ####

Depending on the installation method, files may not be residing in suitable locations. Note that the Debian distribution described above 
uses appropriate paths by default<sup>[3](#footnote03)</sup>. For other install methods, see the [official documentation][link09].

####



Footnotes
---------
<a name="footnote01">1</a>: `shasum` might not be installed. If it is not installed, it can be obtained by installing, e.g., 
perl: `sudo apt-get install perl`  
<a name="footnote02">2</a>: This is a '[soft reload][link07]' that updates configurations and generators without interrupting open 
sockets. Here we are running it so that it can pick up the config from the newly installed Elasticsearch  
<a name="footnote03">3</a>: logs: `/var/log/elasticsearch`; data: `/var/lib/elasticsearch`  


[link01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/java.md
[link02]: https://www.elastic.co/support/matrix
[link06]: https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html#deb-repo
[link07]: https://unix.stackexchange.com/questions/364782/what-does-systemctl-daemon-reload-do
[link08]: http://www.yaml.org
[link09]: https://www.elastic.co/guide/en/elasticsearch/reference/current/path-settings.html
