Set Up Django, PostgreSQL, NGINX, Gunicorn on Ubuntu 16.04
==========================================================

1: Preparation
--------------
Start with:
- [a clean Ubuntu 16.04 install][link01]
- with a non-root user configured who is a member of the sudo group
- and logged in as that user


2: Install Required Packages
----------------------------
Start by updating the apt package cache.

Then install the required packages:

```
sudo apt-get install python3-pip python3-dev libpq-dev postgresql postgresql-contrib nginx
```


3: Create the PostgreSQL Database and User
------------------------------------------
Launch an interactive *psql* session:

```
sudo -u postgres psql
```

Then create the database:

```SQL
CREATE DATABASE <project_name>;
```

Then a user:

```SQL
CREATE USER <project_user> WITH PASSWORD '<password>';
```

Modify some connection parameters as per [Django Project][link02]'s recommendations:

```SQL
ALTER ROLE <project_user> SET client_encoding TO 'utf8';
ALTER ROLE <project_user> SET default_transaction_isolation TO 'read committed';
ALTER ROLE <project_user> SET timezone TO 'UTC';
```

Give the new user permission to administer the database:

```SQL
GRANT ALL PRIVILEGES ON DATABASE <project_name> TO <project_user>;
```

Then quit out of *psql*:

```
\q
```


4: Create a Python Virtual Environment for the Project
------------------------------------------------------

Start by upgrading *pip* and installing *venv*:

```
sudo -H pip3 install --upgrade pip
sudo apt-get install python3-venv
```

(Note: for a discussion of why the `-H` switch is important, see [this][link03] Stack Overflow answer)

(**Question: Why, when this upgrades pip from 8.1.1 to 9.0.1 is the version of pip associated with the subsequently-created Python virtual environment still 8.1.1 instead of 9.0.1?**)

Create a directory to hold the project, *cd* into it, and create the virtual environment:

```
mkdir <project_name>
cd <project_name>
python3 -m venv <project_name_env>
```

Then activate the virtual environment:

```
source <project_name_env>/bin/activate
```

Use the activated, local version of pip to install the other required components:

```
pip install django gunicorn psycopg2
```


5: Create and Configure a New Django Project
--------------------------------------------

Start by telling Django to create its files in the directory we created:

```
django-admin.py startproject <project_name> ~/<project_name>
```

(Note, we are explicitly defining the project directory (`~/<project_name>`) instead of allowing django to decide. By default 
django would have created an extra level of hierarchy)

Edit the settings:

```
vi ~/<project_name>/<project_name>/settings.py
```

and set the `ALLOWED_HOSTS` value (this is a whitelist for which hosts are permitted to connect to the Django instance):

```Python
ALLOWED_HOSTS = ['.<domain>.com', '193.27.45.109']
```

(Note: This should be the domain and IP address of the server hosting the Django project)

Change the `DATABASES` value:

```Python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': '<project_name>',
        'USER': '<project_user>',
        'PASSWORD': '<password>',
        'HOST': 'localhost',
        'PORT': '',
    }
}
```

Add a line to specify where static files should be placed (for NGINX) immediately after the existing `STATIC-URL` line:

```Python
STATIC_ROOT = os.path.join(BASE_DIR, 'static/')
```
[link01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/LinodeAdminChecklist.md
[link02]: https://docs.djangoproject.com/en/1.11/ref/databases/#optimizing-postgresql-s-configuration "Django Documentation: Optimizing PostgreSQL's Configuration"
[link03]: https://stackoverflow.com/questions/43623025/what-does-sudo-h-do "Stack Overflow: What does sudo -H do?"
