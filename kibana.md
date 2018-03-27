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
    shasum kibana-6.2.3-adm64.deb
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
Note that Kibana should be 


[intro01]: https://www.elastic.co/guide/en/kibana/current/introduction.html
[intro02]: https://www.elastic.co/guide/en/kibana/current/setup.html
[install01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/elasticsearch.md
[install02]: https://www.elastic.co/guide/en/kibana/current/setup.html
[install03]: https://www.elastic.co/guide/en/kibana/current/install.html
[activate01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/elasticsearch.md
