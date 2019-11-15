LINUX NOTES
===========

Standardisation Across Distributions
------------------------------------

There are two key standards that most distributions adopt, the
[**Filesystem Hierarchy Standard (FHS)**][path01] and the
[**Linux Standard Base (LSB)**][linf01]. Complying with
these will ensure that an application works across most distributions.

The FHS specifies where files should be stored on a filesystem, thus enabling
software and users to predict the location of files and directories.

The LSB specifies an application binary interface (ABI) for programs that
are compiled and packaged for LSB-conforming implementations for specific
hardware architectures.

### FHS ###

#### The Root Filesystem (`/`) ####

This is the filesystem that must be adequate to boot, restore, recover, or
repair a system.

The following directories (or symlinks to directories) are required in `/`:

Directory | Description
----------|------------
`bin`     | Essential command binaries
`boot`    | Static files of the boot loader
`dev`     | Device files
`etc`     | Host-specific system configuration
`lib`     | Essential shared libraries and kernel modules
`media`   | Mount point for removable media
`mnt`     | Mount point for mounting a filesystem temporarily
`opt`     | Add-on application software packages
`sbin`    | Essential system binaries
`srv`     | Data for services provided by the system
`tmp`     | Temporary files
`usr`     | Secondary hierarchy
`var`     | Variable data

Additionally, the following directories (symlinks) must be in `/` if the
corresponding subsystem is installed:

Directory  | Description
-----------|------------
`home`     | User home directories
`lib<qual>`| Alternate format essential shared libraries
`root`     | Home directory for the root user

Note that regardless of the physical partitioning, all mounted partitions
will be logically under `/`.

#### Common Partitioning Considerations ####

The following are suggested separate partitions (with comments from
_Soyinka: Linux Administration - A Beginner's Guide_):

Directory | Considerations
----------|----------------
`/`       | This is the ultimate parent logical partition. All other partitions are mounted to this.
`boot`    | This contains the files necessary to boot the system (before the kernel loads). Note that `/boot` cannot be created and is not readable using the LVM partition type. A system using LVM will still have a `/boot` mount point but outside of the LVM partitions.
`home`    | Where user home directories are stored. Having this as a separate partition is useful to prevent users from filling up the disk, leaving the server without space for critical functions (like logging).
`usr`     | Where user-installed programs go.
`tmp`     | For temporary files. Ordinary users have write access here so we need this to be on its own partition to prevent users denying the system critical space.
`var`     | This is where log files are saved. It is important that this be on a separate partition so that, e.g., DDOS attacks cannot fill the primary disk of the server.
Swap      | For optimum performance the swap file should be on its own partition. Systems with less than 4GB of memory benefit from having Swap set at double physical memory (need to confirm if this is all still true).

Each partition is mounted at boot time. For example, in the above
implementation, `/` will have a subdirectory called `usr` (as required by the
FSH), but this directory will be empty. When the `/usr` partition is mounted,
users who `cd` into the `/usr` directory will see the contents of the `/usr`
partition.



[linf01]: https://wiki.linuxfoundation.org/lsb/start
[path01]: http://www.pathname.com/fhs/
