DNS Configuration
=================

Using old-style Gandi
---------------------

- Create a new zone file
- Add a new record:
    - Type: A
    - TTL: (leave as default)
    - Name: <my_hostname>
    - Value: <my_ip_address>
- Save the record
- Select "Use this Zone file"


Check Propagation
-----------------

We can use a DNS Propagation Checker to see the status of our new DNS Record, e.g., 
[What's My DNS](https://www.whatsmydns.net/#A/lucy.koumparossoftware.com)
