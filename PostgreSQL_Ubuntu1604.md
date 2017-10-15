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


### a: Create a Unix user ###



[link01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/LinodeAdminChecklist.md
