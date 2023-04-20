# TCP Throughput

## TCP trace segment graph

`tcptrace` is a tool written by [Shawn Ostermann](http://oucsace.cs.ohio.edu/~osterman/) at Ohio University, http://tcptrace.org. Wireshark can produce nice interactive graphs.

![FreeBSD](img/freebsd-newreno.png)

NewReno congestion control, slow start after packet loss.

All graphs below are from Linux using CUBIC cc, running under [Mininet](http://mininet.org).

## Slow sender

`iperf3 -c server -b 10M`

![sender](img/client-limit.png)

## Slow sender using FQ pacing, no bursts

![sender-fq](img/client-limit-fq.png)

## Slow receiver or small snd_wnd

![recv](img/sndwnd-limit.png)

## Small sndbuf

![sndbuf](img/sndbuf-limit.png)

## Bandwidth limit

![bandwidth](img/bandwidth-limit.png)

