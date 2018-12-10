dmesg
=====

Intro
-----
The `dmesg` command reads kernel messages from the in-memory kernel ring 
buffer<sup>[1](#intro01)</sup>, and writes the values to stdout.

Usage
-----
We can read the whole buffer by simply executing the command without any args:
```console
$ dmesg
...
[529491.403760] [UFW BLOCK] IN=eth0 OUT= MAC=f2:3c:91:16:49:54:84:78:ac:57:aa:c1:08:00 SRC=107.170.76.217 DST=104.237.150.176 LEN=40 TOS=0x00 PREC=0x00 TTL=251 ID=17964 PROTO=TCP SPT=65535 DPT=8545 WINDOW=1024 RES=0x00 SYN URGP=0
[529497.606319] [UFW BLOCK] IN=eth0 OUT= MAC=f2:3c:91:16:49:54:84:78:ac:57:a8:41:08:00 SRC=77.72.82.22 DST=104.237.150.176 LEN=40 TOS=0x00 PREC=0x00 TTL=249 ID=12844 PROTO=TCP SPT=40040 DPT=57802 WINDOW=1024 RES=0x00 SYN URGP=0
[529516.630098] [UFW BLOCK] IN=eth0 OUT= MAC=f2:3c:91:16:49:54:84:78:ac:57:a8:41:08:00 SRC=185.254.123.111 DST=104.237.150.176 LEN=40 TOS=0x00 PREC=0x00 TTL=244 ID=54321 PROTO=TCP SPT=56849 DPT=123 WINDOW=65535 RES=0x00 SYN URGP=0
[529518.141732] [UFW BLOCK] IN=eth0 OUT= MAC=f2:3c:91:16:49:54:84:78:ac:57:aa:c1:08:00 SRC=185.222.209.36 DST=104.237.150.176 LEN=40 TOS=0x00 PREC=0x00 TTL=247 ID=123 PROTO=TCP SPT=65533 DPT=3424 WINDOW=1024 RES=0x00 SYN URGP=0
[529531.836862] [UFW BLOCK] IN=eth0 OUT= MAC=f2:3c:91:16:49:54:84:78:ac:57:aa:c1:08:00 SRC=185.254.123.116 DST=104.237.150.176 LEN=40 TOS=0x00 PREC=0x00 TTL=244 ID=18109 PROTO=TCP SPT=46185 DPT=3070 WINDOW=1024 RES=0x00 SYN URGP=0
[529541.383599] [UFW BLOCK] IN=eth0 OUT= MAC=f2:3c:91:16:49:54:84:78:ac:57:a8:41:08:00 SRC=77.72.82.22 DST=104.237.150.176 LEN=40 TOS=0x00 PREC=0x00 TTL=249 ID=29233 PROTO=TCP SPT=40040 DPT=57685 WINDOW=1024 RES=0x00 SYN URGP=0
[529668.449428] [UFW BLOCK] IN=eth0 OUT= MAC=f2:3c:91:16:49:54:84:78:ac:57:aa:c1:08:00 SRC=193.238.47.5 DST=104.237.150.176 LEN=40 TOS=0x00 PREC=0x00 TTL=245 ID=31985 PROTO=TCP SPT=41780 DPT=2567 WINDOW=1024 RES=0x00 SYN URGP=0
[529706.102609] [UFW BLOCK] IN=eth0 OUT= MAC=f2:3c:91:16:49:54:84:78:ac:57:aa:c1:08:00 SRC=159.203.40.26 DST=104.237.150.176 LEN=40 TOS=0x00 PREC=0x00 TTL=245 ID=60431 PROTO=TCP SPT=54430 DPT=3128 WINDOW=1024 RES=0x00 SYN URGP=0
```

If we wanted to dump the whole buffer to a file, we could simply use output
redirection:
```console
$ dmesg > output_file.txt
```

We can search the buffer for any references to, say, USB:
```console
$ dmesg | grep -i usb
```


Logging
-------
On older systems, the contents of the kernel ring buffer at the completion of 
boot are written to `/var/log/dmesg`.

[On new systems using systemd, these messages are not written to a text log file
but are instead captured by journald][sxau_01].

If we want to read just the kernal messages in the journal, we can pass the 
`-k` flag to journalctl:
```console
$ sudo journalctl -k
-- Logs begin at Wed 2018-10-17 04:31:33 MDT, end at Sun 2018-12-09 22:43:14 MST
Dec 03 19:30:22 comp kernel: Linux version 4.15.0-42-generic (buildd@lgw01-amd64
Dec 03 19:30:22 comp kernel: Command line: BOOT_IMAGE=/boot/vmlinuz-4.15.0-42-ge
Dec 03 19:30:22 comp kernel: KERNEL supported cpus:
Dec 03 19:30:22 comp kernel:   Intel GenuineIntel
Dec 03 19:30:22 comp kernel:   AMD AuthenticAMD
Dec 03 19:30:22 comp kernel:   Centaur CentaurHauls
...
```


Footnotes
---------
1. <a name="intro01"> </a>A ring buffer is simply a buffer of fixed sized where,
   when the buffer is full, the newest item overwrites the oldest existing item.





[sxau_01]: https://askubuntu.com/questions/859816/where-did-the-functionality-of-var-log-dmesg-go-in-xenial-16-04
