Set Up Django, PostgreSQL, NGINX, Gunicorn on Ubuntu 16.04
==========================================================

1: Preparation
--------------

Start with:
- a clean Ubuntu 16.04 install
- with a non-root user configured who is a member of the sudo group
- and logged in as that user

2: Install Required Packages
----------------------------
Start by updating the apt package cache.

Then install the required packages:

```
sudo apt-get install python3-pip python3-dev libpq-dev postgresql postgresql-contrib nginx
```

