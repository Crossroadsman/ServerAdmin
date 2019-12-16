Provisioning And Deploying
==========================

1: Definitions
--------------
- *provision*: make the server able to host the code;
- *deploy*: put the code on the server so that it is accessible remotely.

Note, a rule of thumb for distinguishing between *provisioning* and *deploying*: if you need to be root, you are provisioning, otherwise
you are deploying.


2: Example Server Layout
------------------------
```
/
├── etc/
│    ├── nginx/
│    │    ├── sites-available/
│    │    │    ├── DOMAIN1
│    │    │    └── DOMAIN2
│    │    └── sites-enabled/
│    │        ├── DOMAIN1 (symlink to the same-named file in `sites-available`)
│    │        └── DOMAIN2 (symlink to the same-named file in `sites-available`)
│    │
│    └── systemd/system/
│         ├── gunicorn-DOMAIN1.service
│         └── gunicorn-DOMAIN2.service
│
└── home/username/
     └── sites/
          ├── DOMAIN1/
          │    ├── .env
          │    ├── db.sqlite3
          │    ├── manage.py etc
          │    ├── static
          │    └── venv
          └── DOMAIN2/
               ├── .env
               ├── db.sqlite3
               ├── etc...
```


3: Process
----------
1. [ ] [Create clean server with non-root user](https://github.com/Crossroadsman/ServerAdmin/blob/master/LinodeAdminChecklist.md)
2. [ ] [Check the project's `deploy_tools/provisioning_notes` to determine required server-side installs](#s3.2)
3. [ ] [Configure any server-side installs](#s3.3)
4. [ ] [Run the deploy script](#s3.4a) or follow the [manual deployment instructions](#s3.4b)

<a name="s3.2"> </a>
### 3.2: Provisioning Notes ###

- Look at the project's `deploy_tools/provisioning_notes` to see what packages are required.
  Look for a `Required Packages` section that looks something like:
  ```
  Required Packages
  -----------------
  
  - [ ] Nginx (installed globally)
  - [ ] Python (>= 3.6) (installed globally)
  - [ ] Venv (`python3-venv`) (installed globally)
  - [ ] Git (installed globally)
  - [ ] Django (>= 2.2) (installed in the venv of the site)
  - [ ] Gunicorn (installed in the venv of the site)
  ```

- Install the server-side packages. For example: nginx:
  ```console
  $ sudo apt install nginx
  $ sudo systemctl start nginx  # we should now be able to see the nginx test page on a web browser
  ```

- Note that the Ubuntu server base install of Python includes neither `venv` nor `pip`.

<a name="s3.3"> </a>
### 3.3: Configure Server-Side Installs ###

#### 3.3.1: nginx ####

- In the above example, we needed to install [nginx][guide_nginx]. The `deploy_tools/provisioning_notes.md` should
  have a section with configuration notes for these applications.

  Example:
  ```
  Nginx Virtual Host Config
  -------------------------
  - see nginx.template.conf
  - replace DOMAIN with, e.g., staging.mysite.com
  - replace USERNAME with the appropriate Unix username
  ```

- Here is an example `nginx.template.conf`:
  ```nginx
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

- We would create a file called, e.g., `staging.mysite.com` in `/etc/nginx/sites-available` based on the specified template.

- Enable the site by creating a symlink in `sites-enabled` to the config in `sites-available`:
  ```console
  $ SITENAME=staging.mysite.com
  $ cd /etc/nginx/sites-enabled
  $ ln -s /etc/nginx/sites-available/$SITENAME $SITENAME
  $ sudo systemctl reload nginx  # tell nginx to reload its config
  ```

<a name="s3.4a"> </a>
### 3.4(a): Deploy Script ###

- The local repo should have Fabric installed in the venv
- Assuming there is a `fabfile.py` in the `deploy_tools` directory, we can run `fab` to execute the deployment script.

<a name="s3.4b"> </a>
### OR 3.4(b): Manual Deployment ###
TODO

[guide_nginx]: https://github.com/Crossroadsman/ServerAdmin/blob/master/nginx.md
