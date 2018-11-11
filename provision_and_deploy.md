Provisioning And Deploying
==========================

1. Definitions
--------------
- *provision*: make the server able to host the code;
- *deploy*: put the code on the server so that it is accessible remotely.

Note, a rule of thumb for distinguishing between *provisioning* and *deploying*: if you need to be root, you are provisioning, otherwise
you are deploying.


2. Example Server Layout
------------------------
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


3. Process
----------
1. [Create clean server with non-root user](https://github.com/Crossroadsman/ServerAdmin/blob/master/LinodeAdminChecklist.md)
2. [Check the project's `deploy_tools/provisioning_notes` to determine required server-side installs](#s3.2)
3. [Configure any server-side installs](#s3.3)


### <a name="s3.2">3.2 Server-Side Installs</a> ###

- Look at the project's `deploy_tools/provisioning_notes` to see what packages are required. Look for a `Required Packages` section
  that looks something like:
  ```
  Required Packages
  -----------------
  
  - nginx
  - Python (>=3.6)
  - Venv (python3-venv)
  - Git
  - (venv) Django (>=2.0)
  - (venv) Gunicorn
  ```

- Install the server-side packages. For example: nginx:
  ```console
  $ sudo apt install nginx
  $ sudo systemctl start nginx  # we should now be able to see the nginx test page on a web browser
  ```

- Note that the Ubuntu server base install of Python includes neither `venv` nor `pip`.

### <a name="s3.3">3.3 Configure Server-Side Installs</a> ###

- In the above example, we needed to install [nginx]][guide_nginx]. The `deploy_tools/provisioning_notes.md` should have a section 
  with configuration notes for these applications.

  Example:
  ```
  Nginx Virtual Host Config
  -------------------------
  - see nginx.template.conf
  - replace DOMAIN with, e.g., staging.mysite.com
  - replace USERNAME with the appropriate Unix username
  ```

- Here is an example `nginx.template.conf`:
  ```
  server {
    listen 80;
    server_name DOMAIN;

    location /static {
        alias /home/USERNAME/sites/DOMAIN/static_root;
    }

    location / {
        proxy_pass http://unix:/tmp/DOMAIN.socket;
        proxy_set_header Host $host;
    }
  }
  ```




[guide_nginx]: https://github.com/Crossroadsman/ServerAdmin/blob/master/nginx.md
