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
- `head`
- `tail`
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
  ```bash
  $ echo "foo bar baz" | xargs mkdir
  $ ls -l
  drwxr-xr-x  2 testuser  staff  64 17 Oct 16:56 bar
  drwxr-xr-x  2 testuser  staff  64 17 Oct 16:56 baz
  drwxr-xr-x  2 testuser  staff  64 17 Oct 16:56 foo
  ```

Other
-----
- `hostname`  
  `localhost`  
  return the computer's network name
