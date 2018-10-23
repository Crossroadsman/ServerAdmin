Basic Filesystem Navigation
---------------------------
- `cd`
- `cp`
- `mv`
- `rm`
- `mkdir`
- `rmdir`
- `ls`
- `pwd`

More Filesystem Navigation
--------------------------
- `pushd <dirname>`  
  Sample output:  
  `<dirname> ~/temp ~/temp2`  
  `<dirname> $`  
  Explanation:  
  push `<dirname>` onto the `{dirs}` stack (array) and cd to it

- `pushd +n`  
  Sample output:  
  `~ ~/temp ~/temp2`  
  Explanation:  
  bring the dir at index `n` to the top of the stack (rotate the stack) and cd 
  to it

- `popd`  
  pop the `{dirs}` stack

- `popd +n`  
  remove the dir at index `n`

- `dirs [-v]`

File reading
------------
- `cat`
- [`head`](http://linuxcommand.org/lc3_man_pages/head1.html)
- [`tail`](http://linuxcommand.org/lc3_man_pages/tail1.html)
- `less`
- `grep [options] pattern [file]`

File transfer
-------------
- `scp [-P <port>] <source_user>@<source_domain>:<source_path> <destination_user>@<destination_domain>:<destination_path>`

Permissions
-----------
There are three principal permissions classes in a Unix system: `User`, `Group`, `Other`. When a file is created, the owner 
is the user who created it and the owning group is the user's current group.

- [`chmod [options] permissions file`](https://www.computerhope.com/unix/uchmod.htm)  
  Changes permissions of files/directories  
  Examples:
    - `chmod u=rwx,g=rx,o=r myfile`
    - `chmod 754 myfile`

- [`chown [options] {new owner} file ...`](https://www.computerhope.com/unix/uchown.htm)  
  Changes the ownership of a specified file.  
  `{new owner}` takes the form `user[:group]`

Pipeline
--------
- [`xargs`](https://shapeshed.com/unix-xargs/)  
  Used to build an execution pipeline from standard input. Allows tools like `echo`, `rm`, `mkdir` to accept standard input.
  By default, `xargs` reads items from standard input as items separated by blanks. It then runs the associated command once
  for each item.  
  Example:
  ```console
  $ echo "foo bar baz" | xargs mkdir
  $ ls -l
  drwxr-xr-x  2 testuser  staff  64 17 Oct 16:56 bar
  drwxr-xr-x  2 testuser  staff  64 17 Oct 16:56 baz
  drwxr-xr-x  2 testuser  staff  64 17 Oct 16:56 foo
  ```

Other Tools Particularly Well Suited to Pipelines
-------------------------------------------------
- [`sort`](http://linuxcommand.org/lc3_man_pages/sort1.html)
  Sorts standard input then outputs the result to standard output.
- [`uniq`](http://linuxcommand.org/lc3_man_pages/uniq1.html)
  Given a **sorted** stream of data from standard input, removes duplicate lines before passing to standard output.
- [`fmt`](http://linuxcommand.org/lc3_man_pages/uniq1.html)
- [`pr`](http://linuxcommand.org/lc3_man_pages/pr1.html)
  Convert a text file for printing.
- [`tr`](http://linuxcommand.org/lc3_man_pages/tr1.html)
  translate or delete characters.
- [`sed`](http://linuxcommand.org/lc3_man_pages/sed1.html)
  Stream editor. Like `tr` but more sophisticated.
- [`awk`](http://linuxcommand.org/lc3_man_pages/gawk1.html)
  A C-like minilanguage for pattern scanning and processing of files.

Process Management
------------------
- [`ps [opts]`](http://linuxcommand.org/lc3_man_pages/ps1.html)  
  List a current snapshot of running processes (c.f. `top` which continuously updates).  
  We can use the `x` option to include in the list processes that are owned by the current user but were not launched by 
  the current shell. We can use the `a` option to include in the list processes that are not owned by the current user
  (but were launched in the current session). Combining `a` and `x` will list all processes that are running.
  Example:  
  ```console
  $ ps
    PID TTY          TIME CMD
  15765 pts/0    00:00:00 bash
  17311 pts/0    00:00:00 ps
  ```
  Example with `x`:  
  ```console
  $ ps x
    PID TTY      STAT   TIME COMMAND
  15646 ?        Ss     0:00 /lib/systemd/systemd --user
  15647 ?        S      0:00 (sd-pam)
  15764 ?        S      0:00 sshd: tempUser@pts/0
  15765 pts/0    Ss     0:00 -bash
  17338 pts/0    T      0:00 sleep 1000
  ```
  Note that this gives us an additional column: STAT.
- [`kill [signal] pid`](http://linuxcommand.org/lc3_man_pages/kill1.html)  
  Send a signal to a current process (typically to kill the process). Specifying no signal value sends the default, SIGTERM.
  Using the `-L` flag lists the available signals.
  Example:  
  ```console
  $ kill -9 12731 17311 # Send signal 9 (KILL) to PIDs 12731 and 17311
  ```
- `jobs`
  list running processes
- `bg [job]`
  resume the specified paused job and send it to the background
- `fg [job]`
  resume the specified paused job and bring it to the foreground
- CTRL Z  
  stop (pause) a foreground job
- `kill -STOP [job id | pid]`  
  stop (pause) a background job  
  Examples:
  ```console
  $ kill -STOP 17331 # pause the job with the pid 17331
  $ kill -STOP %2 # pause the job with the job id 2
  ```
- `<command> &`  
  Start the process in the background.  


Other
-----
- `hostname`  
  Example output:  
  `localhost`  
  Explanation:   
  return the computer's network name

- `type <command>`  
  `type` is a shell builtin that reports the type of the specified command.  
  Examples:  
  ```console
  $ type type
  type is a shell builtin
  $ type ls  # macOS (at least as of 10.13.6)
  ls is hashed (/bin/ls)
  $ type ls  # Ubuntu 18.04.1 LTS (Linode)
  ls is aliased to `ls --color=auto'
  $ type cp
  cp is /bin/cp
  ```
  
- `which <command>`  
  determine the path to a specified executable (doesn't return anything for shell builtins).  
  Examples:  
  ```console
  $ which ls
  /bin/ls
  $ which type  # a builtin not an executable
  ```

- `help [-m] <builtin>`  
  Get a manpage-like help description for shell builtins. Optional flag `-m` changes the output format.  
  Examples:  
  ```console
  $ help cd
  cd: cd [-L|[-P [-e]] [-@]] [dir]
    Change the shell working directory.

    Change the current directory to DIR.  The default DIR is the value of the
    HOME shell variable.
  <...>
  $ help -m cd
  NAME
    cd - Change the shell working directory.

  SYNOPSIS
    cd [-L|[-P [-e]] [-@]] [dir]
  <...>
  ```
