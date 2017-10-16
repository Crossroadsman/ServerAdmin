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


[link01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/LinodeAdminChecklist.md
[link02]: https://docs.djangoproject.com/en/1.11/ref/databases/#optimizing-postgresql-s-configuration "Django Documentation: Optimizing PostgreSQL's Configuration"
