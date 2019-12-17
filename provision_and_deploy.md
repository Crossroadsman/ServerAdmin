Provisioning And Deploying
==========================

0: Index
--------
- [1: Definitions](#s1)
- [2: Example Server Layout](#s2)
- [3: Process Checklist](#s3)


<a name="s1"> </a>
1: Definitions
--------------
- *provision*: make the server able to host the code;
- *deploy*: put the code on the server so that it is accessible remotely.

Note, a rule of thumb for distinguishing between *provisioning* and *deploying*: if you need to be root, you are provisioning, otherwise
you are deploying.


<a name="s2"> </a>
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

<a name="s3"> </a>
3: Process
----------
- [ ] 3.1: [Create clean server with non-root user](https://github.com/Crossroadsman/ServerAdmin/blob/master/LinodeAdminChecklist.md)
- [ ] 3.2: [Check the project's `deploy_tools/provisioning_notes` to determine required server-side installs](#s3.2)
- [ ] 3.3: [Configure any server-side installs](#s3.3)
- [ ] 3.4: [Run the deploy script](#s3.4a) or follow the [manual deployment instructions](#s3.4b)

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

- In the above example, we needed to install [nginx][guide_nginx]. The `deploy_tools/provisioning_notes.md`
  should have a section with configuration notes for these applications.

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

- We can replace the instances of `DOMAIN` with the actual domain values by doing something like:
  ```console
  server-directory$ cat ./deploy_tools/nginx.template.conf \
      | sed "s/DOMAIN/staging.mysite.com/g" \
      | sed "s/USERNAME/myuser/g" \
      | sudo tee /etc/nginx/sites-available/staging.mysite.com > /dev/null
  ```

- Enable the site by creating a symlink in `sites-enabled` to the config in `sites-available`:
  ```console
  $ SITENAME=staging.mysite.com
  $ cd /etc/nginx/sites-enabled
  $ ln -s /etc/nginx/sites-available/$SITENAME $SITENAME
  $ sudo systemctl reload nginx  # tell nginx to reload its config
  ```

#### 3.3.2: Gunicorn systemd Service ####

- In the above example, we needed to install [Gunicorn][guide_gunicorn]. The `deploy_tools/provisioning_notes.md`
  should have a section with configuration notes for these applications.

  Example:
  ```
  Gunicorn systemd Service
  ------------------------
  - see `gunicorn-systemd.template.service`
  - replace `DOMAIN` with, e.g., `staging.mysite.com`
  - replace `USERNAME` with the appropriate Unix username
  - replace `APPNAME` with the appropriate application name
  ...
  ```

- Here is an example `gunicorn-systemd.template.service`:
  ```gunicorn
  [Unit]
  Description=Gunicorn server for DOMAIN

  [Service]
  Restart=on-failure
  User=USERNAME
  WorkingDirectory=/home/USERNAME/sites/DOMAIN
  EnvironmentFile=/home/USERNAME/sites/DOMAIN/.env

  ExecStart=/home/USERNAME/sites/DOMAIN/venv/bin/gunicorn \
      --bind unix:/tmp/DOMAIN.socket \
      APPNAME.wsgi:application

  [Install]
  WantedBy=multi-user.target
  ```

- replace `DOMAIN` with, e.g., `staging.mysite.com`, `USERNAME` with the appropriate Unix username,
  `APPNAME` with the appropriate application name:
  ```console
  server$ cat ./deploy_tools/gunicorn-systemd.template.service \
      | sed "s/DOMAIN/staging.mysite.com/g" \
      | sed "s/USERNAME/myuser/g" \
      | sed "s/APPNAME/myapplicationname/g" \
      | sudo tee /etc/systemd/system/gunicorn-staging.mysite.com.service
  ```

- start the service immediately: `sudo systemctl start gunicorn`

- set the service to start on boot: `sudo systemctl enable gunicorn`

- to reload the service:
  - first reload the service definition file: `sudo systemctl daemon-reload`
  - then restart gunicorn: `sudo systemctl restart gunicorn`



<a name="s3.4a"> </a>
### 3.4(a): Deploy Script ###

- The local repo should have Fabric installed in the venv
- Assuming there is a `fabfile.py` in the `deploy_tools` directory, we can run `fab` to execute the deployment script.

<a name="s3.4b"> </a>
### OR 3.4(b): Manual Deployment ###

- After copying deployed files to server:
  - [ ] `collectstatic`
  - [ ] `makemigrations` and `migrate`
  - [ ] may need to restart nginx and/or gunicorn



[guide_nginx]: https://github.com/Crossroadsman/ServerAdmin/blob/master/nginx.md
[guide_gunicorn]: https://github.com/Crossroadsman/ServerAdmin/blob/master/gunicorn.md
