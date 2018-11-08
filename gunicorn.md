Gunicorn (Green Unicorn)
========================

1: Background
-------------
[Gunicorn][link01] is an app server inspired by Ruby's Unicorn. It is the second tier in the three-tier architecture of:
```
.-----------,       .----------,       .--------,
|web  server| <---> |app server| <---> |database|
|  (nginx)  |       |(gunicorn)|       | (sql)  |
`-----------'       `----------'       `--------'
```

All http requests come into the web server. The web server first handles malicious or other bad requests, then decides whether good
requests are for static or dynamic content. For static files it just serves them directly, for dynamic requests it passes the request
to the app server. (QUESTION: DOES THE APP SERVER GET THE ORIGINAL REQUEST OR A SANITISED REQUEST?)

Then app server then executes the app code (like Django does in the testing environment) to determine, and then create, the http 
response, which it then sends back to nginx to provide to the remote client. (QUESTION: DOES THE APP SERVER SEND BACK TO NGINX OR
DOES IT SERVE THE RESPONSE DIRECTLY TO THE CLIENT?)

For a more longer, but still very simple explanation from a gunicorn developer, see [this](https://serverfault.com/a/331263) 
Server Fault answer.

2: Installation
---------------
- Gunicorn is a Python application so we can install it using pip from inside our [virtual environment][link02]:
  ```
  pip install gunicorn
  ```
- Test the installation by `cd`ing to the project's root directory and load Gunicorn (binding it to the project's WSGI file):
  ```
  gunicorn --bind 0.0.0.0:8000 <my_project>.wsgi
  ```
  This will connect the `wsgi.py` file in the `<my_project>` subdirectory to Gunicorn.

3: Create a System Service
--------------------------
Having established that we can connect Gunicorn to the Django project, we now need to create a system service for starting and stopping the 
Gunicorn server.

- Create a new `systemd` service file:
  ```
  sudo vi /etc/systemd/system/gunicorn.service
  ```
 
- Inside the file add the following code:
  ```
  [Unit]
  Description=gunicorn daemon
  After=network.target
  
  [Service]
  User=<unix_user>
  Group=www-data
  WorkingDirectory=/home/<unix_user>/<project_name>
  ExecStart=/home/<unix_user>/<project_name>/<venv_name>/bin/gunicorn/ --access-logfile - --workers 3 --bind unix:/home/<unix_user>/<project_name>/<project_name>.sock <project_name>.wsgi:application
  
  [Install]
  WantedBy=multi-user.target
  ```
  
  **Notes**:
  - The `[Unit]` section in a systemd service file contains the metadata and dependencies for a service;
  - `Description` is metadata that describes the service _**(NTF: how is this description accessed?)**_;
  - `After` specifies a dependency, in this case which services need to already be running before this service can start
  - The `[Service]` section describes all the details for how the service is activated;
  - The `User` is the unix user who will be the owner of the service (the user in whose home directory the Django project is installed);
  - The `Group` is the unix group who will be the owner of the service (here we are using a new group `www-data` that will make it 
    convenient for Gunicorn to communicate with nginx);
  - `WorkingDirectory` is the full path to the root folder for the project;
  - `ExecStart` is the full path to the Gunicorn executable (installed in our virtual environment) and all the switches passed to that
    executable:
    - `--access-logfile -`: _**(NTF: what does `-` mean?)**_;
    - `--workers 3`: Use 3 worker processes;
    - `--bind unix:/home/<unix_user>/<project_name>/<project_name>.sock <project_name>.wsgi:application`: Because Gunicorn is running on
      the same computer as Django we can bind the Django app to Gunicorn using a local unix socket instead of a port (which is what we 
      did when testing the Gunicorn install above);
  - The `[Install]` section is for specifying what boot types should cause this service to launch;
  - `WantedBy=multi-user.target` specifies that this service should start if the system is booted as a normal multi-user system.

We can now start the service to run it now:
```
sudo systemctl start gunicorn
```

And enable it to run at bood:
```
sudo systemctl enable gunicorn
```

We can test the status of the service:
```
sudo systemctl status gunicorn
```

And check that the `sock` file was created in the project's root folder:
```
ls /home/<unix_user>/<project_name>
```

If the service isn't running or if the service didn't create a sock file we can investigate by looking at the logs:
```
sudo journalctl -u gunicorn
```

[Here][link03] are some common problems that might cause startup to fail.

After fixing any problems, reload the service definition file:
```
sudo systemctl daemon-reload
```
and then restart Gunicorn:
```
sudo systemctl restart gunicorn
```


[link01]: http://gunicorn.org
[link02]: https://github.com/Crossroadsman/ServerAdmin/blob/master/PostgresWithDjango.md
[link03]: https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu-16-04
