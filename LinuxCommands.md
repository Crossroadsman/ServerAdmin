Basic File Navigation
---------------------
- `cd`
- `cp`
- `mv`
- `rm`
- `mkdir`
- `rmdir`
- `ls`
- `pwd`

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
