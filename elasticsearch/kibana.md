Kibana
======

Introduction
------------
Kibana is an [analytics and visualisation platform designed to work with Elasticsearch][intro01]. It [runs on node.js][intro02].

Installation
------------
- Start with a [properly configured system with an Elasticsearch instance][install01]
- [Download][install03] the Kibana version that [corresponds to the installed Elasticsearch version][install02]:
  - Manual installation with Debian:  
    ```
    wget https://artifacts.elastic.co/downloads/kibana/kibana-6.2.3-amd64.deb
    wget https://artifacts.elastic.co/downloads/kibana/kibana-6.2.3-amd64.deb.sha512
    shasum -a 512 -c kibana-6.2.3-adm64.deb.sha512
    sudo dpkg -i kibana-6.2.3-amd64.deb
    ```

Activation
----------
Like Elasticsearch, Kibana doesn't start automatically when installed.
- [Determine whether we are using SysV `init` or `systemd`][activate01]
- Set Kibana to start automatically on boot as follows:  
  ```
  sudo /bin/systemctl daemon-reload
  sudo /bin/systemctl enable kibana.service
  ```

Note, Kibana can be started and stopped using:
```
sudo systemctl start kibana.service
sudo systemctl stop kibana.service
```

Configuration
-------------
Kibana's config file is in `/etc/kibana/kibana.yml`.

By default Kibana runs on `localhost:5601`

Common settings to consider changing (shown with defaults):
- `elasticsearch.preserveHost: true` - When set to `true`, Kibana will only use the hostname specified in the `server.host` setting.
  When `false`, uses the hostname of the host that connects to the Kibana instance;
- `elasticsearch.url: "http://localhost:9200"` - The URL of the Elasticsearch instance;
- `elasticsearch.username`, `elasticsearch.password` - If the Elasticsearch instance is protected with basic security then the credentials
   go here;
- `server.defaultRoute: "/app/kibana"` - The default route when opening Kibana;
- `server.host: "localhost"` - The host for the back end server;
- `server.name: "<name>"` - Human-readable name to identify this instance;
- `server.port: 5601` - Kinbana is served by a back-end server, this specifies the port.



[intro01]: https://www.elastic.co/guide/en/kibana/current/introduction.html
[intro02]: https://www.elastic.co/guide/en/kibana/current/setup.html
[install01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/elasticsearch.md
[install02]: https://www.elastic.co/guide/en/kibana/current/setup.html
[install03]: https://www.elastic.co/guide/en/kibana/current/install.html
[activate01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/elasticsearch.md
