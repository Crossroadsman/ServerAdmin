Keeping a Process Running When Disconnected
===========================================

The Issue
---------

When a remote terminal session is closed (and in some other cases), the kernel sends the `SIGHUP` (Signal Hang-up) command to the 
Shell.

When it receives `SIGHUP`, Bash sends `SIGHUP` to all jobs (both foreground and background) (if the session is interactive) except 
`disown`ed jobs.

On receiving `SIGHUP`, processes such as Django stop.


The Solutions
-------------

There are three most commonly suggested solutions to this issue:

- launch the process you want to keep alive with **Screen**;
- launch the process with the `nohup` command;
- launch the process with Bash's builtin `disown`.

### Screen ###

#### Why? ####

- Screen is a very comprehensive tool that can keep the process alive and much more.

#### Why not? ####

- Screen may be overkill if the only goal is to keep a process alive when a VPS disconnects;
- Needs to be installed on most systems.

#### How ####

##### Install Screen #####

```
$ sudo apt-get install screen
```

##### Run Screen #####

```
$ screen
> <process_name>
```

then send `^A d` to exit from the Screen session


##### Resume a Screen session later #####

```
$ screen -r
```

### nohup ###

#### Why? ####

- Works with any Linux system without installing any extra software;
- Narrowly solves the specific problem of `SIGHUP` causing the process to be terminated (`nohup` intercepts `SIGHUP`)

#### Why not? ####

#### How ####

```
$ nohup <process_name> &
$ exit
```


### disown ###

#### Why? ####

- Builtin for Bash, so if running the Bash shell doesn't require installing any extra software.

#### Why not? ####

- Only works with Bash.

#### How ####

##### Get the process name of the relevant job #####

Send `^Z` to suspend foreground process.
You will see a report that lists the process that looks something like:

```
[1]+    Stopped                processName
```

##### Send the relevant job to the background #####
(This will resume the suspended job in the background)

```
$ bg %<process_number>
```

##### Disown the process #####

```
$ disown -h <PID>|%<process_number>
```

The `-h` flag preserves ownership of the process and only `disown`s on termination of the current shell session.
`disown` can take either a PID or the process number from the previous step as the identifier.





For more information see: 
- <https://stackoverflow.com/questions/32780706/does-linux-kill-background-processes-if-we-close-the-terminal-from-which-it-has>
- <https://unix.stackexchange.com/questions/479/keep-ssh-sessions-running-after-disconnection>
