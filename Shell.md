The Unix Shell
==============

What is the Shell
-----------------
Shell is the program that enables the user to directly interact with the OS. 
Much of the following applies to various common Unix shells, but specific 
examples and syntax are only guaranteed to apply to [Bash][link01]


The Role of the Shell
---------------------
- Reading text and parsing entered commands
- Evaluating metacharacters (such as wildcards, etc)
- Processing i/o redirection, pipes, background processing
- Signal handling
- Initializing programs for execution


[Shell Syntax / Operation][link02]
----------------------------------
1. Read input from
   - file;
   - string supplied as an argument to the `-c` invocation option; or
   - from user's terminal input;
2. Break the input into tokens (*words* and *operators*); perform *alias expansion*
3. Parse the tokens into commands
4. Perform *shell expansions*
5. Perform redirections (and remove redirection operators and operands from the argument list)
6. Execute the command
7. (Optionally) wait for the command to complete and collect its exit status


Expansion
---------
The shell performs certain expansions after a command is submitted and before 
the command is executed. Consider the following simple example:
```console
$ echo *
MyFiles  Temp
```
Here, `*` was expanded by the shell before the command was executed. In 
particular, `*` is expanded to the names of the files in the current directory.

```console
$ echo ~
/home/tempUser
```
Here. `~` is expanded to the user's home directory (more accurately, it expands 
to the specified user's home directory, or the current user if no user is 
specified. I.e., `~alice` expands to `/home/alice` if user alice exists (will 
just return `~alice` as a literal if there is no user alice)).  
The shell can also perform basic integer arithmetic using the form `$((a + b))`:
```console
$ echo $((2 + 2))
4
```

We can also use braces to perform expansion. See a bunch of examples in 
[this](http://linuxcommand.org/lc3_lts0080.php) guide. Two are reproduced below:
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
A current list of environment variables is available by running `printenv` or 
`set`<sup>[1](#footnote01)</sup>.

We can use command substitution to use the output of a command as an expansion.
```console
$ ls -l $(which cp)
-rwxr-xr-x 1 root root 71516 2007-12-05 08:58 /bin/cp
```
(This is equivalent to running `ls -l /bin/cp`)

[Escaping and Quoting][link03]
------------------------------
- `"` (double quotes)  
  Items enclosed in double quotes will treat most special characters as 
  literals.
  Exceptions include `$`, `\`<sup>[2](#footnote02)</sup>, and `` ` ``, which 
  can each be escaped with a single backslash.  
  Another exception is `!`<sup>[3](#footnote03)</sup> which is more complex to 
  escape (see example 2).  
  The best solution seems to be to wrap the text either side of the `!` in 
  double quotes and the `!` alone in single quotes.  
  Example 1:  
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
  Example 2:  
  ```console
  $ echo "hello!? are you there?"
  -bash: !? are you there?: event not found
  $ echo "hello\!? are you there?"  # the backslash won't entirely help us here
  hello\!? are you there?
  $ echo "hello"'!'"? are you there?"
  hello!? are you there?
  ```

- single quote  
  Items enclosed in single quotes will have **all** expansions suppressed. This
  means that a single quote character can never be represented in a string 
  enclosed in single quotes (even if escaped).  
  Example:  
  ```console
  $ echo '$(cal)'
  $(cal)
  ```

- backslash  
  Escapes a single character. It preserves the literal value of the next 
  character that follows (except `\\n`, i.e., backslash followed by newline, 
  which represents line continuation)
  Example 1:  
  ```console
  $ echo "The balance for user $USER is: \$5.00"
  The balance for user tempUser is: $5.00
  ```
  Example 2:
  ```console
  $ echo "This is a long string that is going to be spread over multiple lines \
  of the terminal input, lorem ipsum dolor sit amet, laudem blandit usu in. \
  Assum bonorum honestatis sea ad, vivendum corrumpit ut sed. Habeo legere \
  scriptorem cu sit. Cu natum fierent molestie est, expetenda definiebas \
  honestatis nam ea, est ex oratio reprimique. No cetero legimus \
  scribentur mel."
  This is a long string that is going to be spread over multiple lines of the te
  rminal input, lorem ipsum dolor sit amet, laudem blandit usu in. Assum bonorum
   honestatis sea ad, vivendum corrumpit ut sed. Habeo legere scriptorem cu sit.
   Cu natum fierent molestie est, expetenda definiebas honestatis nam ea, est ex
   oratio reprimique. No cetero legimus scribentur mel.
  ```


Chaining (Listing) Commands[link04]
-----------------------------------
- `command1 && command2` AND list: *command2* is executed iff *command1* 
  returns an exit status 0
- `command1 || command2` OR<sup>[4](#footnote04)</sup> list: *command2* is 
  executed iff *command1* returns an exit status not 0

See also [this](https://askubuntu.com/a/817969) Ask Ubuntu answer which provides 
more interesting commentary on chaining commands.


Aliases and Functions
---------------------
### Alias ###
Alias is a shell builtin that allows creating named aliases to commands. As a 
builtin, created aliases are only available to the current session (although 
they can be specified in one of the shell's startup scripts to load 
automatically).
Syntax: `alias name=value`.  
Examples:  
```console
$ alias la='ls -la'
```

```console
$ alias today='date +"%FT%T%:z"' #2018-10-23T17:12:02+01:00
2018-10-23T17:12:02+01:00
```

### Shell Functions ###
Example:
```console
today() {
    echo -n "Today's date is: "
    date +"%FT%T%:z"
}
```


Footnotes
---------
<a name="footnote01">1</a>: `printenv` and `set` differ in that `set` (a shell builtin) can see shell-local variables 
(i.e., variables that are local to the shell, including shell functions) while `printenv` can only see exported variables (the
variables that are passed to all programs).
<a name="footnote02">2</a>: Only retains special meaning if followed by `$`, 'backtick', `"`, `\`, or *newline*  
<a name="footnote03">3</a>: Only when history expansion is enabled and shell is not in Posix mode
<a name="footnote04">4</a>: It is called 'OR' but is really XOR





[link01]: https://www.gnu.org/software/bash/manual/bash.html#SEC_Contents
[link02]: https://www.gnu.org/software/bash/manual/bash.html#Shell-Operation
[link03]: https://www.gnu.org/software/bash/manual/bash.html#Quoting
[link04]: https://www.gnu.org/software/bash/manual/bash.html#Lists
