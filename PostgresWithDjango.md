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
sudo apt-get install python3-pip python-dev libpq-dev postgresql postgresql-contrib
```

3: Create a DB and DB User
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

Cerate a database user who will connect to, and interact with, the DB:

```SQL
CREATE USER <project_user> WITH PASSWORD '<password>';
```

Specify some connection parameters for the user so that they don't have to be set on each session:

```SQL
ALTER ROLE <project_user> SET client_encoding TO 'utf-8';
ALTER ROLE <project_user> SET default_transaction_isolation TO 'read committed';
ALTER ROLE <project_user> SET timezone TO 'UTC';
```

Give the new user access rights to the database:

```SQL
GRANT ALL PRIVILEGES ON DATABASE <project_name> TO <project_user>;
```

Finally, exit the interactive session:

```
\q
```


4: Install Django in a Virtual Environment
------------------------------------------

Add the venv module:

```
sudo apt-get install python3-venv
```

Create a new directory to hold the project:

```
mkdir <project_name>
```

Inside that directory create a new virtual environment:

```
python3 -m venv <project_name_env>
```

Load the virtual environment:

```
source <project_name_env>/bin/activate
```

(should modify prompt to show the specified virtual environment)

Ensure you are using the most recent version of pip:

```
pip install --upgrade pip
```

(Because we are in a virtual environment that has created a link for pip to the pip3 binary, we can just type pip)

Install Django and pyscopg2 (the Python-PostgreSQL database adapter) using pip:

```
pip install django psycopg2
```

(Note you can view the details of a particular package by entering `pip show <package_name>`)

Create a Django project in the <project_name> directory:

```
django-admin.py startproject <project_name> .
```

(Note the `.` at the end of the line)





[link01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/LinodeAdminChecklist.md
