# Profiling Linux TCP/IP stack with perf and pprof

At home, I have two Linux hosts with Mellanox 10GbE nic (bought used from Ebay in 2017),
directly connected using SPF cable.

![10gbe](img/10gbe.jpg)

Thoughput was about 1100MiB/s, both machine runs ~40% CPU utilization in one thread.

## Tx path

Profile taken on Debian bullseye (testing, pre-release 11) w/ kernel 5.6.14.
Running the [chargen](https://github.com/chenshuo/recipes/blob/master/tpc/bin/chargen.cc) program
to keep sending data to a `discard` server.

[![chargen](img/profile-chargen.png)](img/profile-chargen.html)

## Rx path

Profile taken on Ubuntu 18.04 w/ kernel 4.15
Running the [discard](https://github.com/chenshuo/recipes/blob/master/tpc/bin/discard.cc) program
to keep reading the socket.

[![discard](img/profile-discard.png)](img/profile-discard.html)
