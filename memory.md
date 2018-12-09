Memory
======

Memory Management in Linux
--------------------------
TODO

Virtual Memory
--------------
TODO

Over-Commitment
---------------
TODO

Out-of-Memory (OOM) Killer
--------------------------
TODO

How to Tell if Server Ran Out of Memory
---------------------------------------
12345678901234567890123456789012345678901234567890123456789012345678901234567890
It's unusual that a server will completely run out of memory, because Linux 
will start using the OOM killer before that point is reached.

We can see memory usage using the `free` command:
```console
$ free -h
              total        used        free      shared  buff/cache   available
Mem:          985Mi       328Mi       329Mi       6.0Mi       327Mi       513Mi
Swap:         511Mi          0B       511Mi
```

If the server crashed, we can review the logs from right before it crashed to
see if they indicate out of memory.
```console
$ less /var/log/messages
```

Note that a crash situation means that `dmesg` won't help, since it only shows
kernel messages since the last boot. If the server didn't actually crash, but
expected services/applications aren't running, we can use `dmesg` to check if
the OOM killer terminated out services:
```console
$ dmesg | grep -Ei "oom"
```
