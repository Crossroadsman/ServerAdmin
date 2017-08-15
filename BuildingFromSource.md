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

```
curl -O <url_to_tarball>
```

(`-O` is an alias for `--remote-name` and signifies that the next argument will be the remote url)

3: Extract Tarball
------------------

```
tar -xvf <filename>
```

Key:
- `x`: extract
- `v`: verbose
- `f`: filename

4: Prepare `makefile` (etc) with configure
------------------------------------------

```
./configure
```

5: Use `make` to build the application
--------------------------------------

Run from the project's source directory.

```
make
```
