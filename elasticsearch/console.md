Console (Formerly Sense)
========================

Console, formerly known as Sense, is a [Kibana][intro01] app that [provides an interactive console for submitting requests to 
Elasticsearch direct from a browser][intro02].


Installation
------------
From Kibana v5.0, Console is included in the base Kibana install. For older versions, Sense is installed as follows:

- Start with a [properly configured Elasticsearch/Kibana instance][install01];
- Run the following command in the Kibana directory<sup>[1](#install01)</sup> to download and install Sense:  
  ```
  ./bin/kibana-plugin install elastic/sense
  ```
  
  
Footnotes
---------
<a name="install01">1</a>: Assuming a Debian-package install, the Kibana home directory is `/usr/share/kibana`


[intro01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/kibana.md
[intro02]: https://www.elastic.co/guide/en/elasticsearch/guide/master/running-elasticsearch.html#sense
[install01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/kibana.md
