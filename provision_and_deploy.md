Provisioning And Deploying
==========================

Definitions
-----------
- *provision*: make the server able to host the code;
- *deploy*: put the code on the server so that it is accessible remotely.

Note, a rule of thumb for distinguishing between *provisioning* and *deploying*: if you need to be root, you are provisioning, otherwise
you are deploying.


Example Server Layout
---------------------
```
/home/unixuser
├── sites
|   ├── staging.mysite.com
|   │   ├── db
|   │   ├── manage.py
|   │   ├── <...>
|   │   ├── static
|   │   │   ├── css
|   │   │   └── js
|   │   └── venv
|   ├── www.mysite.com
|   │   ├── db
|   │   ├── manage.py
|   │   ├── <...>
|   │   ├── static
|   │   │   ├── css
|   │   │   └── js
|   │   └── venv
.
.
.
```

