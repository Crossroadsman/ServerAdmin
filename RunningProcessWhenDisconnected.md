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


### nohup ###

#### Why? ####

- Works with any Linux system without installing any extra software;
- Narrowly solves the specific problem of `SIGHUP` causing the process to be terminated (`nohup` intercepts `SIGHUP`)

#### Why not? ####

#### How ####

### disown ###


#### Why? ####

- Builtin for Bash, so if running the Bash shell doesn't require installing any extra software.

#### Why not? ####

- Only works with Bash.

#### How ####




For more information see: 
- <https://stackoverflow.com/questions/32780706/does-linux-kill-background-processes-if-we-close-the-terminal-from-which-it-has>
- <https://unix.stackexchange.com/questions/479/keep-ssh-sessions-running-after-disconnection>
