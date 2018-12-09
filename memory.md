Memory
======

Memory Management in Linux
--------------------------
TODO

Virtual Memory
--------------
TODO
See also [Mel Gorman: "Understanding the Linux Virtual Memory Manager][gorm_01].

Over-Commitment
---------------
Many distributions set the default value of `/proc/sys/vm/overcommit_memory` to
zero, which means processes can request more memory than is currently free in the
system (on the usually-true assumption that applications will generally use less
memory than they've requested).

You can check the setting on a system as follows:
```console
$ cat /proc/sys/vm/overcommit_memory
0
```

A value of `0` or `1` means overcommitment is enabled, `2` means overcommitment
is disabled.

See also: [Unix & Linux: Will Linux start killing my processes without asking 
me if memory gets short?][sxul_01]


Out-of-Memory (OOM) Killer
--------------------------
From the [Out of Memory Management Chapter][gorm_02] of [Gorman][gorm_01]:

> Before deciding to kill a process, it [the system] goes through the following 
> checklist:
>
> - Is there enough swap space left (`nr_swap_pages` > 0)? If yes, not OOM
> - Has it been more than 5 seconds since the last failure? If yes, not OOM
> - Have we failed within the last second? If no, not OOM
> - If there hasn't been 10 failures at least in the last 5 seconds, we're 
>   not OOM
> - Has a process been killed within the last 5 seconds? If yes, not OOM

If none of the above tests indicate not OOM, `oom_kill()` will be called to 
determine a process to kill.

The `select_bad_process()` function is responsible for deciding which process
will be killed. It does this by going through every running task and calculating
a score with the `badness()` function.

To illustrate how it works, here are the comments in the `badness()` 
function's [source][linm_01]:
> The main rationale is that we want to select a good task to kill when we run 
> out of memory.
> 
> Good in this context means that:
> 1. we lose the minimum amount of work done
> 2. we recover a large amount of memory
> 3. we don't kill anything innocent of eating tons of memory
> 4. we want to kill the minimum amount of processes (one)
> 5. we try to kill the process the user expects us to kill, this
>    algorithm has been meticulously tuned to meet the principle
>    of least surprise ... (be careful when you change it)

The final calculated score (out of 1000) has the user-settable value 
of `/proc/<pid>/oom_score_adj`(which itself has a value from -1000 to +1000) 
added to it.

Once the process has been identified, the process list is traversed again and
every process that shares the same `mm_struct` as the selected process (i.e., 
they are threads) is sent a signal (either `SIGTERM` for hardware-accessing
processes, or `SIGKILL` otherwise).


How to Tell if Server Ran Out of Memory
---------------------------------------
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
or:
```console
$ dmesg | grep -Ei "killed process"
```

[If a process was killed you'll see a line like](https://www.memset.com/docs/additional-information/oom-killer/):
```
host kernel: Out of Memory: Killed process 2592 (mysql).
```

[or][sxul_01]:
```
[11686.043641] Out of memory: Kill process 2603 (flasherav) score 761 or sacrifice child
[11686.043647] Killed process 2603 (flasherav) total-vm:1498536kB, anon-rss:721784kB, file-rss:4228k
```




[gorm_01]: https://www.kernel.org/doc/gorman/html/understand/index.html "kernel.org: Understanding the Linux Virtual Memory Manager"
[gorm_02]: https://www.kernel.org/doc/gorman/html/understand/understand016.html "kernel.org: Chapter 13 Out of Memory Management"
[linm_01]: https://linux-mm.org/OOM_Killer
[sxul_01]: https://unix.stackexchange.com/a/136294
