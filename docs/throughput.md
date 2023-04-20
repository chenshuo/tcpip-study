# TCP Throughput

## TCP trace segment graph

`tcptrace` is a tool written by [Shawn Ostermann](http://oucsace.cs.ohio.edu/~osterman/) at Ohio University, http://tcptrace.org. Wireshark can produce nice interactive graphs.

![FreeBSD](img/freebsd-newreno.png)

NewReno congestion control, slow start after packet loss.


## Mininet

All graphs below are from Linux using CUBIC cc, running under [Mininet](http://mininet.org).

```
mininet> net
h1 h1-eth0:s1-eth1
h2 h2-eth0:s1-eth2
s1 lo:  s1-eth1:h1-eth0 s1-eth2:h2-eth0
```

TODO: default topo


```
mininet> h1 ping -c 4 h2
PING 10.0.0.2 (10.0.0.2) 56(84) bytes of data.
64 bytes from 10.0.0.2: icmp_seq=1 ttl=64 time=0.252 ms
64 bytes from 10.0.0.2: icmp_seq=2 ttl=64 time=0.053 ms
64 bytes from 10.0.0.2: icmp_seq=3 ttl=64 time=0.059 ms
64 bytes from 10.0.0.2: icmp_seq=4 ttl=64 time=0.056 ms
```

```
mininet> xterm h2  # then start `iperf3 -s` on h2

mininet> h1 iperf3 -c h2
Connecting to host 10.0.0.2, port 5201
[  5] local 10.0.0.1 port 37680 connected to 10.0.0.2 port 5201
[ ID] Interval           Transfer     Bitrate         Retr  Cwnd
[  5]   0.00-1.00   sec  8.00 GBytes  68.7 Gbits/sec    0    666 KBytes
[  5]   1.00-2.00   sec  8.20 GBytes  70.4 Gbits/sec    0    940 KBytes
[  5]   2.00-3.00   sec  7.89 GBytes  67.7 Gbits/sec    0   1.15 MBytes
[  5]   3.00-4.00   sec  7.90 GBytes  67.9 Gbits/sec    0   1.28 MBytes
[  5]   4.00-5.00   sec  7.98 GBytes  68.6 Gbits/sec    0   1.42 MBytes
[  5]   5.00-6.00   sec  8.16 GBytes  70.1 Gbits/sec    0   1.70 MBytes
[  5]   6.00-7.00   sec  8.17 GBytes  70.2 Gbits/sec    0   1.70 MBytes
[  5]   7.00-8.00   sec  8.14 GBytes  69.9 Gbits/sec    0   1.79 MBytes
[  5]   8.00-9.00   sec  8.04 GBytes  69.0 Gbits/sec    0   1.79 MBytes
[  5]   9.00-10.00  sec  7.99 GBytes  68.7 Gbits/sec    0   1.88 MBytes
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Retr
[  5]   0.00-10.00  sec  80.5 GBytes  69.1 Gbits/sec    0             sender
[  5]   0.00-10.00  sec  80.5 GBytes  69.1 Gbits/sec                  receiver

iperf Done.
```

Add 100ms latency using `netem delay 100ms`.

```
mininet> s1 tc qdisc replace dev s1-eth2 root netem delay 100ms

mininet> h1 ping -c 4 h2
PING 10.0.0.2 (10.0.0.2) 56(84) bytes of data.
64 bytes from 10.0.0.2: icmp_seq=1 ttl=64 time=101 ms
64 bytes from 10.0.0.2: icmp_seq=2 ttl=64 time=100 ms
64 bytes from 10.0.0.2: icmp_seq=3 ttl=64 time=100 ms
64 bytes from 10.0.0.2: icmp_seq=4 ttl=64 time=100 ms
```

## Slow sender

`iperf3 -c server --bitrate 10M`

![sender](img/client-limit.png)

## Slow sender using FQ pacing

`iperf3 -c server --fq-rate 10M`

No bursts.

![sender-fq](img/client-limit-fq.png)

## Slow receiver

`iperf3 -s --server-bitrate-limit 10M`

![receiver](img/server-limit.png)

## Small `snd_wnd`

```
mininet> h1 bin/tcpperf -c h2
Connected 10.0.0.1:37662 -> 10.0.0.2:2009, congestion control: cubic
Time (s)  Throughput   Bitrate    Cwnd    Rwnd  sndbuf  ssthresh  rtt/var
  0.000s   0.00kB/s   0.00kbps  14.1Ki  42.4Ki  85.3Ki    2048Mi  201.2ms/100593
  1.048s   21.0MB/s    168Mbps  10.5Mi  6094Ki  16.0Mi    2048Mi  100.2ms/56  retrans=3
  2.050s   65.2MB/s    522Mbps  16.5Mi  6518Ki  16.0Mi    2048Mi  100.4ms/107
  3.054s   66.6MB/s    533Mbps  16.5Mi  6546Ki  16.0Mi    2048Mi  100.4ms/80
  4.058s   66.7MB/s    534Mbps  16.5Mi  6520Ki  16.0Mi    2048Mi  100.5ms/61
  5.063s   66.7MB/s    533Mbps  16.5Mi  6546Ki  16.0Mi    2048Mi  100.5ms/80
  6.066s   66.8MB/s    534Mbps  16.5Mi  6520Ki  16.0Mi    2048Mi  100.4ms/81
  7.070s   66.6MB/s    533Mbps  16.5Mi  6546Ki  16.0Mi    2048Mi  100.4ms/69
  8.074s   66.8MB/s    534Mbps  16.5Mi  6520Ki  16.0Mi    2048Mi  100.4ms/68
  9.077s   66.9MB/s    535Mbps  16.5Mi  6552Ki  16.0Mi    2048Mi  100.3ms/77
 10.081s   66.7MB/s    534Mbps  16.5Mi  6548Ki  16.0Mi    2048Mi  100.4ms/115
Transferred 623MBytes in 10.238s, 4754 syscalls, 131072.0 Bytes/syscall
```

Throughput is limited by Rwnd (`snd_wnd`), 100ms * 66.7MB/s = 6.5MB.

![recv](img/sndwnd-limit.png)

Set larger `tcp_rmem` on receiver, for larger sndwnd.

```
mininet> h2 sysctl -A |grep tcp_.mem
net.ipv4.tcp_rmem = 10240	87380	16777216
net.ipv4.tcp_wmem = 10240	87380	16777216
mininet> h2 sysctl -A |grep tcp_adv
net.ipv4.tcp_adv_win_scale = 1
mininet> h2 sysctl -w net.ipv4.tcp_rmem="10240 131072 65536000"
net.ipv4.tcp_rmem = 10240 131072 65536000
```

For `net.ipv4.tcp_adv_win_scale = 1`, `Rwnd = tcp_rmem[2] / 2 = 32MB`.


## Small sndbuf

```
mininet> h1 bin/tcpperf -c h2
Connected 10.0.0.1:45092 -> 10.0.0.2:2009, congestion control: cubic
Time (s)  Throughput   Bitrate    Cwnd    Rwnd  sndbuf  ssthresh  rtt/var
  0.000s   0.00kB/s   0.00kbps  14.1Ki  42.4Ki  85.3Ki    2048Mi  100.7ms/50326
  1.045s   18.3MB/s    147Mbps  10.2Mi  8474Ki  16.0Mi    2048Mi  103.6ms/6137  retrans=2
  2.034s    148MB/s   1184Mbps  31.4Mi  31.2Mi  16.0Mi    2048Mi  100.0ms/20
  3.014s    159MB/s   1272Mbps  31.4Mi  31.2Mi  16.0Mi    2048Mi  100.0ms/1
  4.005s    157MB/s   1255Mbps  31.4Mi  31.2Mi  16.0Mi    2048Mi  100.0ms/5
  5.042s    158MB/s   1264Mbps  31.4Mi  31.2Mi  16.0Mi    2048Mi  100.0ms/16
  6.020s    159MB/s   1275Mbps  31.4Mi  31.2Mi  16.0Mi    2048Mi  100.0ms/15
  7.016s    154MB/s   1229Mbps  31.4Mi  31.2Mi  16.0Mi    2048Mi  100.0ms/2
  8.055s    158MB/s   1261Mbps  31.4Mi  31.2Mi  16.0Mi    2048Mi  100.0ms/0
  9.019s    164MB/s   1314Mbps  31.4Mi  31.2Mi  16.0Mi    2048Mi  100.0ms/15
 10.034s    151MB/s   1206Mbps  31.4Mi  31.2Mi  16.0Mi    2048Mi  100.0ms/14
Transferred 1425MBytes in 10.134s, 10869 syscalls, 131072.0 Bytes/syscall
```

Throughput is limited by sndbuf, 100ms * 160MB/s = 16MB.


![sndbuf](img/sndbuf-limit.png)

Higher throughput achived by larger `sndbuf`.

```
mininet> h1 sysctl -w net.ipv4.tcp_wmem="10240 131072 65536000"
net.ipv4.tcp_wmem = 10240 131072 65536000

mininet> h1 bin/tcpperf -c h2
Connected 10.0.0.1:53176 -> 10.0.0.2:2009, congestion control: cubic
Time (s)  Throughput   Bitrate    Cwnd    Rwnd  sndbuf  ssthresh  rtt/var
  0.000s   0.00kB/s   0.00kbps  14.1Ki  42.4Ki   128Ki    2048Mi  100.4ms/50205
  1.004s   32.1MB/s    257Mbps  7767Ki  11.0Mi  37.8Mi    2048Mi  100.5ms/47
  2.051s    249MB/s   1995Mbps  68.3Mi  24.9Mi  62.5Mi    2048Mi  100.7ms/134  retrans=585
  3.056s    260MB/s   2081Mbps  68.3Mi  24.9Mi  62.5Mi    2048Mi  100.6ms/147
  4.061s    260MB/s   2082Mbps  68.3Mi  21.9Mi  62.5Mi    2048Mi  100.5ms/23
  5.024s    274MB/s   2194Mbps  68.3Mi  24.3Mi  62.5Mi    2048Mi  100.2ms/64
  6.028s    313MB/s   2500Mbps  68.3Mi  26.4Mi  62.5Mi    2048Mi  100.1ms/58
  7.034s    325MB/s   2603Mbps  68.3Mi  29.6Mi  62.5Mi    2048Mi  100.1ms/73
  8.040s    325MB/s   2602Mbps  68.3Mi  28.9Mi  62.5Mi    2048Mi  100.1ms/40
  9.046s    326MB/s   2604Mbps  68.3Mi  29.7Mi  62.5Mi    2048Mi  100.2ms/112
 10.051s    325MB/s   2603Mbps  68.3Mi  27.9Mi  62.5Mi    2048Mi  100.1ms/100
Transferred 2703MBytes in 10.154s, 20625 syscalls, 131072.0 Bytes/syscall
```

## Small Cwnd

TODO:

1. clamp

2. packet loss


## Bandwidth limit

```
mininet> s1 tc qdisc replace dev s1-eth2 root netem delay 10ms rate 10Mbit

mininet> h1 bin/tcpperf -c h2
Connected 10.0.0.1:48982 -> 10.0.0.2:2009, congestion control: cubic
Time (s)  Throughput   Bitrate    Cwnd    Rwnd  sndbuf  ssthresh  rtt/var
  0.000s   0.00kB/s   0.00kbps  14.1Ki  42.4Ki   128Ki    2048Mi  10.7ms/5341
  1.120s   1638kB/s   13.1Mbps   132Ki   323Ki   790Ki    70.7Ki  106.2ms/574
  2.094s   1481kB/s   11.8Mbps   189Ki   416Ki  1139Ki    70.7Ki  152.8ms/1331
  3.018s   1418kB/s   11.3Mbps   243Ki   535Ki  1462Ki    70.7Ki  196.7ms/1030
  4.226s   1411kB/s   11.3Mbps   314Ki   680Ki  1887Ki    70.7Ki  254.5ms/1929
  5.196s   1351kB/s   10.8Mbps   370Ki   795Ki  2227Ki    70.7Ki  300.2ms/813
  6.357s   1354kB/s   10.8Mbps   438Ki   955Ki  2635Ki    70.7Ki  355.6ms/856
  7.009s   1408kB/s   11.3Mbps   475Ki  1021Ki  2856Ki    70.7Ki  386.2ms/374
  8.444s   1461kB/s   11.7Mbps   560Ki  1213Ki  3366Ki    70.7Ki  455.5ms/1334
  9.281s   1409kB/s   11.3Mbps   608Ki  1326Ki  3655Ki    70.7Ki  494.9ms/801
 10.160s   1342kB/s   10.7Mbps   660Ki  1399Ki  3970Ki    70.7Ki  537.0ms/1078
Transferred 14.5MBytes in 12.180s, 111 syscalls, 131072.0 Bytes/syscall
```

![bandwidth](img/bandwidth-limit.png)

