Building From Source
====================

1: Initial System Configuration
--------------------------------

Start by downloading build tools. On Ubuntu we use `apt-get` and the build tools are `build-essential`


Refresh the system's list of available packages using `update`:

```
sudo apt-get update
```

Then download and install the build tools:

```
sudo apt-get install build-essential
```

Next, find `make` (one of the components in `build-essential`)

This will confirm that the build tools were installed:

```
which make
```

If installed, should get a terminal response like:

```
/usr/bin/make
```


2: Download Remote Source Tarball
---------------------------------

Decide where we are going to store the source files, `/home/source` is a common choice:
```
sudo mkdir /home/source
sudo mkdir /home/source/tarballs
```

Whatever location we choose, we can create a symlink to that location and give it a common name, e.g., `/source`:
```
sudo ln -s /home/source /source
```

We can `chown` the directory tree so we don't always need to be superuser:
```
sudo chown -R <username> /home/source
```
(Note this will make `/home/source` and `/home/source/tarballs` owned by `<username>` but the `/source` symlink will still be owned by
root).

We then download our tarball:
```
cd /source/tarballs
curl -O <url_to_tarball>
```
(`-O` is an alias for `--remote-name` and signifies that we want the local filename to be the same as the remote filename)

Or if we prefer Gnu wget:
```
cd /source/tarballs
wget <url_to_tarball>
```

3: Check md5 hash
-----------------

[Verify][link01] that the md5 is valid


4: Extract [Tarball][link02]
----------------------------

```
tar -xvf <filename>
```

Key:
- `x`: extract
- `v`: verbose
- `f`: filename


5: Prepare `makefile` (etc) with configure
------------------------------------------

```
./configure
```


6: Use `make` to build the application
--------------------------------------

Run from the project's source directory.

```
make
```


7: Install the program
----------------------

```
sudo make install
```




[link01]: https://github.com/Crossroadsman/TerminalTips/blob/master/md5sum.md
[link02]: https://github.com/Crossroadsman/TerminalTips/blob/master/commands/tar.md
