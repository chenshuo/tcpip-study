# TCP Slow Start

Standard slow start [RFC5681](https://www.rfc-editor.org/rfc/rfc5681.html#section-3.1):

* Cwnd increases nMSS upon receipt of an ACK covering new data of nMSS.
* Effectively Cwnd doubles every round-trip time, Cwnd = IW * 2 ^ nRTT.

With Initial Window = 10:

![](img/slow-start.png)

Linux 4.20 changed `tcp_rmem[1]` from 87k to 128KiB, [commit by Yuchung Cheng in 2018-09](https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/commit/?id=a337531b942bd8a03e7052444d7e36972aac2d92).
So SYN rwin increased from 43k to 65k.

With IW=10 and MSS=1.4k, during slow start,
the old setting will hit window full on 3rd RTT,
limit sent bytes to ~70kB.
In new setting, 1.4 * (10+20+40) ~= 100k can be sent in 3RTT.

![](img/slow-start-10ms.png)

![](img/slow-start-10ms-large-rwnd.png)

## HyStart++

* <https://datatracker.ietf.org/doc/html/draft-ietf-tcpm-hystartplusplus>

* <https://blog.cloudflare.com/cubic-and-hystart-support-in-quiche/>

