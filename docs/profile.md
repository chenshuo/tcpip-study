# Profiling Linux TCP/IP stack with perf and pprof

At home, I have two Linux hosts with Mellanox 10GbE nic (bought used from Ebay in 2017),
directly connected using SPF cable.

![10gbe](img/10gbe.jpg)

Thoughput was about 1100MiB/s, both machine runs ~40% CPU utilization in one thread.

## Tx path

Profile taken on Debian bullseye (testing, release 11) w/ kernel 5.6.14.
![chargen](img/profile-chargen.png)

## Rx path

Profile taken on Ubuntu 18.04 w/ kernel 4.15
![discard](img/profile-discard.png)
