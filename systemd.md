systemd
=======

What is systemd
---------------
From the [homepage][systemd_home]:  
> systemd is a suite of basic building blocks for a Linux system.  
> It provides a system and service manager that runs as PID 1 and starts the rest of the system.  
> Other parts include a logging daemon, utilities to control basic system configuration like the hostname, date, locale, maintain a list 
> of logged-in users and running containers and virtual machines, system accounts, runtime directories and settings, and daemons to manage 
> simple network configuration, network time synchronization, log forwarding, and name resolution. 


How It Works
------------
systemd decides what to launch by looking at 'service' ('unit') files. These files describe how systemd should execute a particular
service. Note the service itself can be in any format that the system can execute, from shell scripts to application binaries.

Example of a valid systemd service file:
```ini
[Unit]
Description=Gunicorn server for staging.mysite.com

[Service]
Restart=on-failure
User=myuser
WorkingDirectory=/home/myuser/sites/staging.mysite.com
EnvironmentFile=/home/myuser/sites/staging.mysite.com/.env

ExecStart=/home/myuser/sites/staging.mysite.com/venv/bin/gunicorn \
    --bind unix:/tmp/staging.mysite.com.socket \
    superlists.wsgi:application

[Install]
WantedBy=multi-user.target
```

The `Unit` section describes what the file is and (optionally) when to run. The `Service` section describes what we actually want to 
run.

The `Install` section describes positive and negative dependencies in either direction.

Service files must have the `.service` extension and custom service config files (i.e., user-created) are saved in 
`/etc/systemd/system`.

They can be started with `systemctl start myservice.service`.


systemctl Examples
------------------
### Check the version ###
```console
$ systemctl --version
systemd 237
+PAM +AUDIT +SELINUX +IMA +APPARMOR +SMACK +SYSVINIT +UTMP +LIBCRYPTSETUP +GCRYPT +GNUTLS +ACL +XZ +LZ4 +SECCOMP +BLKID +ELFUTILS 
+KMOD -IDN2 +IDN -PCRE2 default-hierarchy=hybrid
```

### Check the Status of Services ###
```console
$ systemctl
...
ufw.service  loaded active       exited      Uncomplicated firewall
ntpd.service loaded maintenance  maintenance Network Time Service
...
```

### Check the Status of a Specific Service ###
```console
$ systemctl status ufw.service
● ufw.service - Uncomplicated firewall
   Loaded: loaded (/lib/systemd/system/ufw.service; enabled; vendor preset: enab
   Active: active (exited) since Wed 2018-11-07 14:18:04 MST; 1 day 5h ago
     Docs: man:ufw(8)
 Main PID: 391 (code=exited, status=0/SUCCESS)
    Tasks: 0 (limit: 1110)
   CGroup: /system.slice/ufw.service

Warning: Journal has been rotated since unit was started. Log output is incomple
```

### View the whole journal of a service ###
(`systemctl status <servicename>` only shows the last few lines of the log)
```console
$ journalctl -u ufw -e
```

### Dependency Analysis ###
View dependencies (things that the specified service depend on):
```console
$ systemctl list-dependencies sshd.service
```

Reverse dependencies (things that depend on the specified service):
```console
$ systemctl list-dependencies --reverse multi-user.target
```

### Service Configs ###
- Reload the system manager configuration  
  [Long answer][systemctl_man]:
  > reload systemd manager configuration. This will rerun all generators (see systemd.generator(7)), reload all unit files, and 
  > recreate the entire dependency tree. 
  
  Note that it "reloads" unit files, so only those unit files that were already loaded<sup>[1](#footnote01)</sup> (i.e., in memory) 
  will be reloaded. Similarly, the `recreate the entire dependency tree` is a recreation based on the dependencies that are loaded 
  (or reloaded)(?). Any unit files that were not already loaded and didn't become dependencies of the reloaded files will remain 
  outside the dependency tree(?).
  
  In short, it seems that the use cases for this command are (mostly) situations where a unit is loaded (or might be loaded) and
  we are modifying the unit configuration. After we modify the config, we run the daemon-reload to reload the unit (and its config)(?).
  This will not restart the unit itself, so if the change to the config file warrants reloading the unit, we need to separately issue
  a restart command.
  ```console
  $ systemctl daemon-reload
  ```

- Start a service right now (and only right now, not on boot)  
  ```console
  $ systemctl start ufw.service
  ```

- Stop a service
  ```console
  $ systemctl stop ufw.service
  ```

- Set a service to run on boot  
  Strictly, `enable` reads the `[Install]` section of the config file, and then updates the various dependencies subdirectories
  with symlinks accordingly. For example, with a setting of `WantedBy=multi-user.target` in `[Install]`, `enable` will ensure that this 
  config file is symlinked from the `/etc/systemd/system/multi-user.target.wants` directory. Thus, when `multi-user.target` (roughly
  equivalent to runlevel 2) is about to be set (i.e., at boot time), systemd will load its dependencies, which are any services with
  config files symlinked from `.../multi-user.target.wants` or `.../multi-user.target.requires`. Conversely, if the specified config
  file's `[Install]` section did not make it a dependency for anything that loads on boot, `enable` would not do anything.  
  More detail in the [man page][systemd_unit]. 
  After the symlinks have been created, the 'system manager configuration' is reloaded. The system manager configuration reload is
  basically equivalent to `systemctl daemon-reload` for just the specified service (as per the [enable section of the 
  systemctl man][systemctl_man]).
  ```console
  $ systemctl enable ufw.service
  ```


Logging ([`journalctl`][journalctl_man])
----------------------------------------
`systemd` units produce binary logs. The typical way to review these logs is using `journalctl`.

By default, journalctl sends its output to less, which gives us the standard interactive controls such as f/b, pg up/pg down, 
cursor up/down to scroll through the lines of the logs. You can use the cursor left and right arrows to view text that has 
been truncated.

Example Log (truncated):
```
-- Logs begin at Sat 2018-11-10 20:26:59 MST, end at Mon 2018-12-03 20:54:12 MST
Dec 03 20:54:12 my_server sudo[2028]: pam_unix(sudo:session): session opened for use
Dec 03 20:54:12 my_server sudo[2028]:     some_user : TTY=pts/0 ; PWD=/home/some_user/sites ;
```

Note the first columns show the date and time (server's local time), then the server's hostname, the process name (with pid),
then the log message.


### Common Options ###

- `-b [n]`: show logs from the current boot. Specify a negative value for `n` to show the logs from the boot n boots ago.
- `-f`: follow (live update)
- `-F <variable>`: show all the available values for the specified variable (handy for constructing filters)
- `-n 100`: only show the most recent 100 entries
- `-o <type>`: specify the output format (examples include `cat`, `json`, `json-pretty`, `short` (default), `verbose`)
- `-p <level>`: filter based on priority level
- `-r`: present results in reverse chronological order
- `-u <service> [-u <another_service>]`: just view entries for the specified service(s)
- `--list-boots`: list the available boots
- `--no-pager`: send logs to stdout without paging
- `--since "2 days ago" | "2018-11-30 14:37:00"`: show entries from a certain point of time (relative or absolute) 
- `--until "8 hours ago" | "2018-12-03 06:00:00"`: show entries to a certain point of time (relative or absolute)
- `--utc`: format timestamps in UTC
- `<VARIABLE>=<value>`: filter entries where the specified variable has the specified value (the variables are as shown when
  viewing the log in verbose mode). For example, suppose a particular log entry of interest has an associated UID of 1000 (shown as 
  `_UID=1000`. We can filter to show only log entries for that UID using `_UID=1000` as an argument to journalctl. Similarly if we
  want to filter by a PID or a group ID we could use `_PID=<value>` or `_GID=<value>`, respectively. Variables starting with a 
  leading underscore are supplied by journald, while variables without a leading underscore were provided by the logged service
  itself. For more information see `man systemd.journal-fields`

### More Guides ###

In addition to the [man page][journalctl_man], there are several useful guides for server admins. For example:
[Lennart Poettering](http://0pointer.de/blog/projects/journalctl.html)
[Linode](https://www.linode.com/docs/quick-answers/linux/how-to-use-journalctl/)
[Digital Ocean](https://www.digitalocean.com/community/tutorials/how-to-use-journalctl-to-view-and-manipulate-systemd-logs)
[Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-using_the_journal)
[Cheatsheet (pdf)](https://www.cheatography.com/airlove/cheat-sheets/journalctl/pdf/)


Footnotes
---------
<a name="footnote01">1.</a> "Loaded" means simply that the unit has been loaded from the disk into memory. This happens any time that 
the unit is 'looked at' which includes running `systemctl status <unit>`. Hence any unit that is examined with `systemctl status` will
always appear to be loaded because it is loaded for `status` (and then immediately unloaded). See 
[this](https://unix.stackexchange.com/a/336890) Linux/Unix Stack Exchange answer and the Unit Garbage Collection section of 
the [systemd.unit][systemd_unit] man page.





[journalctl_man]: https://www.freedesktop.org/software/systemd/man/journalctl.html
[systemd_home]: https://www.freedesktop.org/wiki/Software/systemd/
[systemd_unit]: https://www.freedesktop.org/software/systemd/man/systemd.unit.html#
[systemctl_man]: https://www.freedesktop.org/software/systemd/man/systemctl.html
