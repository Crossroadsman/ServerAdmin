The Unix Shell
==============

What is the Shell
-----------------

Shell is the program that enables the user to directly interact with the OS.

The Role of the Shell
---------------------

- Reading text and parsing entered commands
- Evaluating metacharacters (such as wildcards, etc)
- Processing i/o redirection, pipes, background processing
- Signal handling
- Initializing programs for execution

Expansion
---------
The shell performs certain expansions after a command is submitted and before the command is executed. Consider the following
simple example:
```console
$ echo *
MyFiles  Temp
```
Here, `*` was expanded by the shell before the command was executed. In particular, `*` is expanded to the names of the files 
in the current directory.
```console
$ echo ~
/home/tempUser
```
Here. `~` is expanded to the user's home directory (more accurately, it expands to the specified user's home directory, or the 
current user if no user is specified. I.e., `~alice` expands to `/home/alice` if user alice exists (will just return 
`~alice` as a literal if there is no user alice)).
The shell can also perform basic integer arithmetic using the form `$((a + b))`:
```console
$ echo $((2 + 2))
4
```
We can also use braces to perform expansion. See a bunch of examples in [this](http://linuxcommand.org/lc3_lts0080.php) guide. Two are reproduced below:
```console
$ echo Front-{A,B,C}-Back
Front-A-Back Front-B-Back Front-C-Back
```

```console
$ mkdir {2016..2018}-0{1..9} {2016..2018}-{10..12}
$ ls
2016-01 2016-07 2017-01 2017-07 2018-01 2018-07
2016-02 2016-08 2017-02 2017-08 2018-02 2018-08
2016-03 2016-09 2017-03 2017-09 2018-03 2018-09
2016-04 2016-10 2017-04 2017-10 2018-04 2018-10
2016-05 2016-11 2017-05 2017-11 2018-05 2018-11
2016-06 2016-12 2017-06 2017-12 2018-06 2018-12
```

We can also do parameter/variable expansion:
```console
$ echo $USER
tempUser
```

A current list of environment variables is available by running `printenv`.

We can use command substitution to use the output of a command as an expansion.
```console
$ ls -l $(which cp)
-rwxr-xr-x 1 root root 71516 2007-12-05 08:58 /bin/cp
```
(This is equivalent to running `ls -l /bin/cp`)

Quoting/Escaping
----------------
- double quotes
  Items enclosed in double quotes will treat all special characters as literals (except `$`, `\` and `` ` ``)
  Example:
  ```console
  $ echo $(cal)
  October 2018 Su Mo Tu We Th Fr Sa 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25 26 27 28 29 30 31
  $ echo "$(cal)"
  October 2018
  Su Mo Tu We Th Fr Sa
      1  2  3  4  5  6
   7  8  9 10 11 12 13
  14 15 16 17 18 19 20
  21 22 23 24 25 26 27
  28 29 30 31
  ```
