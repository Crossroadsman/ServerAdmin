Installing and Using PostgreSQL on Ubuntu 16.04
===============================================

1: Installation
---------------

- [Set up a clean Ubuntu 16.04 instance, with a new user in the sudo group][link01]

- Refresh the apt package cache:

```
sudo apt-get update
```

- Install the *postgres* package and the *-contrib* package of additional utilities:

```
sudo apt-get install postgresql postgresql-contrib
```


2: Setting up PostgreSQL
------------------------

We can access the Postgres commands, including *psql* as the postgres Unix user. We can either issue each command using 
`sudo -u postgres` or we can create a new shell as postgres and run all the commands from that user shell:

```
sudo -iu postgres
```

(the `-i` tells sudo to launch a new shell session)

As postgres we can launch a new interactive *psql* session by typing:

```
psql
```

And we can exit that interactive session by issuing the command:

```
\q
```

### a: Create a PostgreSQL user ###

As the postgres unix user we can issue administrative commands at the unix command line (many of which are wrappers around SQL commands
that could be issued inside *psql*).

First, create a new user by issuing the command:

```
createuser --interactive
```

(*createuser* is a wrapper around the SQL command `CREATE ROLE`)

Note, this only creates the PostgreSQL user, not the associated unix user or database.


### b: Create a Database ###

PostgreSQL's default behaviour is to use peer authentication to attempt to automatically log in the current unix user to the PostgreSQL 
user account of the same name, and connect to the database of the same name.

For convenience we will create a database with the same name as the user we created in the previous step (e.g., **testuser**):

From the command line as a user in the sudo group:

```
sudo -u postgres createdb testuser
```

(This is equivalent to typing `createdb testuser` if we were in a shell as the postgres unix user)


### c: Create a Unix user ###

```
sudo adduser testuser
```

Now this unix user can load *psql* and connect automatically to the *testuser* database as the *testuser* PostgreSQL role.

We can test this from *psql* by running the command:

```
\conninfo
```



[link01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/LinodeAdminChecklist.md
