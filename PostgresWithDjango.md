Using PostgreSQL with Django Application
========================================

1: Initial Setup
----------------

- [Create a clean 16.04 server instance][link01]
- Add a non-root user in the sudo group
- Log in as that user


2: Install Python and Components
--------------------------------

- Update the apt package cache:
```
sudo apt-get update
```

- Install components
```
sudo apt-get install python3-pip python3-dev python3-venv
```

3: Install PostgreSQL
---------------------
```
sudo apt-get install libpq-dev postgresql postgresql-contrib
```

4: Create a Project Directory (`<project_name>`)
----------------------------------------------

5: Inside the Project Directory, Create, Activate, and Upgrade a Virtual Environment
------------------------------------------------------------------------------------
```
python3 -m venv <project_name>
source <project_name>/bin/activate
pip install --upgrade pip
```

6: Install Django and Psycopg2
------------------------------
```
pip install django psycopg2
```

7: Create a DB and DB User
--------------------------

Postgres uses 'peer authentication'  for local connections. During installation, a 'postgres' linux user was created to correspond 
to the postgres PostgreSQL user. This user performs administrative tasks. Connect to an interactive session as follows:

```
sudo -u postgres psql
```

Create a database for the project (each project should have its own, isolated, DB):

```SQL
CREATE DATABASE <project_name>;
```

Create a database user who will connect to, and interact with, the DB:

```SQL
CREATE USER <project_user> WITH PASSWORD '<password>';
```

Give the new user access rights to the database:

```SQL
GRANT ALL PRIVILEGES ON DATABASE <project_name> TO <project_user>;
```

8: [Specify some connection parameters for the user so that they don't have to be set on each session][link02]
--------------------------------------------------------------------------------------------------------------

```SQL
ALTER ROLE <project_user> SET client_encoding TO 'UTF8';
ALTER ROLE <project_user> SET default_transaction_isolation TO 'read committed';
ALTER ROLE <project_user> SET timezone TO 'UTC';
```

Finally, exit the interactive session:

```
\q
```

9: Install Django in the Project's Virtual Environment
------------------------------------------------------

Create a Django project in the <project_name> directory:

```
django-admin.py startproject <project_name> .
```

(Note the `.` at the end of the line, which is an optional argument to explicitly set the project directory)

10: Edit the Project Settings
-----------------------------

### a: set `ALLOWED_HOSTS`: ###

```Python
ALLOWED_HOSTS = ['.<domain>.com', '<ip_address>']
```

### b: change the `DATABASES` value: ###

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



[link01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/LinodeAdminChecklist.md
[link02]: https://docs.djangoproject.com/en/1.11/ref/databases/#optimizing-postgresql-s-configuration
