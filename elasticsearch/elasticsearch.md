Elasticsearch
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

Also note that it may take 30 seconds or so for Elasticsearch to be available after restarting it.


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

For production, the following items need to be configured in `/etc/elasticsearch/elasticsearch.yml`:

- Paths
- Cluster and Node name
- Network host
- Discovery (for clustering)
- Heap (size and dump path)
- GC Logging

#### Paths ####
Depending on the installation method, files may not be residing in suitable locations. Note that the Debian distribution described above 
uses appropriate paths by default<sup>[3](#footnote03)</sup>. For other install methods, see the [official documentation][link09].

#### Cluster and Node Name ####
Nodes will only join clusters with the same name.
```
cluster.name: <cluster_name>
```
By default, Elasticsearch gives a new node a name corresponding to the first seven characters of the UUID. To give it a more memorable name:
```
node.name: <node_name>
```
You can give it an absolute value, e.g., `node.name: my_elastic_node` or pass in the computer's HOSTNAME variable: `node.name: ${HOSTNAME}`

#### Network Host ####
By default, Elasticsearch binds to loopback addresses only.
To set the host, specify:
```
network.host: <IP-address>
```
Elasticsearch understands [IPv4 and IPv6 addresses, FQDNs, 0.0.0.0, and some special values like (`_local_`, `_site_`, `_global_`, etc)][link10].

#### Heap ####

##### Size #####
Set the heap in `jvm.options` by specifying minimum (`Xms`) and maximum (`Xmx`) settings.

Note: you can check total memory by typing `sudo lshw -c memory` and see available memory using `free -m`<sup>[4](#footnote04)</sup>.
[Considerations][link11]:
- Set min and max to be equal to each other;
- Higher heap values allow more caching but also lead to longer garbage collection pauses;
- Max should be no more than 50% of physical RAM;
- Max should not be larger than the JVM's zero-based compressed oops value (usually around 26GB)

##### Dump Path #####
By default, some installs of Elasticsearch configure the JVM to dump the heap on out of memory exceptions to `/var/lib/elasticsearch`. If.
for any reason, this path is unsuitable, modify the entry `-XX:HeapDumpPath=<path>` to the preferred path.


Securing
--------
We can specify a firewall rule so that only the trusted host (the host that is running the app using elasticsearch) can access the 
Elasticsearch port:
```
sudo ufw allow from <trusted_host> to any port 9200
```
**Notes**:
- `from <trusted_host>` : only allow the specified host access through the firewall for this rule;
- `to any` : this rule will allow access regardless of what ip address the machine running ufw has (so if the server moves ip address, 
  it won't affect this rule);
- `port 9200` : this rule only applies on port 9200


Add Some Test Data
------------------
Data can typically be added to Elasticsearch one of two ways:
- json over http;
- native client.

### Json over http ###
We can use curl to do an http POST or PUT.
```
curl -XPUT 'http://myserver.com:9200/blog/user/finn' -d '{"name": "Finn Human"}' -H 'Content-Type: application/json'
```
**Notes**:
- `-X<method> <url>` use an alternative method to the default (deafult=GET) for the specified URL. `POST` is used for creating a 
  new document, `PUT` for modifying an existing one
- in the url, Elasticsearch interprets the path (after the port) as `/<index>/<type>/<id>`. A [breaking change in v6.0 is that an index can
  now only have one type][link13]. This means that after the above example, attempting to issue the following:
  `curl -XPUT 'http://myserver.com:9200/blog/post/helloworld' -d '{"title": "Hello World"}' -H 'Content-Type: application/json'`
  will produce an error in v6.0 because we are attempting to create more than one type ('user', 'post') for a single index ('blog').
- `-d <data>` send the specified data in a POST request
- `-H <content-type>` tells Elasticsearch what form the data is in. Note this [only became possible in v5.3 and mandatory in v6.0][link12]
  so many guides and examples do not include this argument.

We can retrieve that entry with an http GET:
```
curl -XGET 'http://myserver.com:9200/blog/user/finn'
```
We can optionally get a more 'pretty' retrieval as follows:
```
curl -XGET 'http://myserver.com:9200/blog/user/finn?pretty'
```

#### PUT vs POST ####
Per the [logz.io tutorial][link14], we use PUT when we want to specify the data item's ID and POST when we want Elasticsearch to generate
it. Examples:
```
curl -X PUT 'elastic.mydomain.com:9200/app/users/7' -H 'Content-Type: application/json' -d '{
  "id": 7, 
  "username": "nice_king", 
  "last_login": "2018-05-03T16:34:32-0600"
}'

curl -X POST 'elastic.mydomain.com:9200/logs/myapp' -H 'Content-Type: application/json' -d '{
	"timestamp": "2018-05-03T16:32:35-0600",
	"message": "Log in attempt",
	"user_id": 7,
	"admin": false
}' 
```

Understanding the Data
----------------------
As is typical with NoSQL databases, we don't need define the structure of the data, although we can improve performance by defining 
mappings for data types.
We can view the indicesas follows:
```
curl -X GET '<url>:9200/_cat/indices?v&pretty'
```

Searching
---------
### Fetch a single record ###
```
curl -X GET '<url>:9200/<index>/<type>/<id>?pretty'
```

### Perform a search query ###

#### [URI Search](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-uri-request.html) ####
```
curl -X GET '<url>:9200/_search?q=finn'
```
or
```
curl -X GET '<url>:9200/_search?q=%22Hello+World%22&pretty'
```

#### Other Search Types ####

- [Request Body Search](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-body.html)
- [Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html) (see also the [logz.io tutorial][link14])


Removing Data
-------------
Delete a record:
```
curl -X DELETE '<url>:9200/<index>/<type>/<id>?pretty'
```

Delete an index:
```
curl -X DELETE '<url>:9200/<index>?pretty'
```



Footnotes
---------
<a name="footnote01">1</a>: `shasum` might not be installed. If it is not installed, it can be obtained by installing, e.g., 
  perl: `sudo apt-get install perl`  
<a name="footnote02">2</a>: This is a '[soft reload][link07]' that updates configurations and generators without interrupting open 
  sockets. Here we are running it so that it can pick up the config from the newly installed Elasticsearch  
<a name="footnote03">3</a>: logs: `/var/log/elasticsearch`; data: `/var/lib/elasticsearch`  
<a name="footnote04">4</a>: Note that Linux allows the disk cache to use otherwise free memory (which will be given over to applications as
  they request it). This will cause the 'free' column in `free` to approach zero even hardly any memory has been allocated to applications.
  Looking at the 'available' column will give an indication of how much memory is really available. See [here](https://www.linuxatemyram.com) 
  for more details.


[link01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/java.md
[link02]: https://www.elastic.co/support/matrix
[link06]: https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html#deb-repo
[link07]: https://unix.stackexchange.com/questions/364782/what-does-systemctl-daemon-reload-do
[link08]: http://www.yaml.org
[link09]: https://www.elastic.co/guide/en/elasticsearch/reference/current/path-settings.html
[link10]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-network.html#network-interface-values
[link11]: https://www.elastic.co/guide/en/elasticsearch/reference/current/heap-size.html
[link12]: https://www.elastic.co/blog/strict-content-type-checking-for-elasticsearch-rest-requests
[link13]: https://discuss.elastic.co/clicks/track?url=https%3A%2F%2Fwww.elastic.co%2Fblog%2Findex-type-parent-child-join-now-future-in-elasticsearch&post_id=387316&topic_id=106089
[link14]: https://logz.io/blog/elasticsearch-tutorial/
