# Nagle's Algorithm

[Nagle's Algorithm](https://en.wikipedia.org/wiki/Nagle%27s_algorithm)

* [RFC896](https://tools.ietf.org/html/rfc896): Congestion Control in IP/TCP Internetworks, John Nagle , 1984-01.
    *  [Minshall's update](https://datatracker.ietf.org/doc/html/draft-minshall-nagle), 1999.


* [The trouble with the Nagle algorithm ](https://developers.slashdot.org/comments.pl?sid=174457&cid=14515105) by John Nagle, 2006.

* [Nginx Optimization: understanding sendfile, tcp_nodelay and tcp_nopush](https://thoughts.t37.net/nginx-optimization-understanding-sendfile-tcp-nodelay-and-tcp-nopush-c55cdd276765)

* In reply to above post, John Nagle on Nagle's algorithm <https://news.ycombinator.com/item?id=9045125>, 2015.

> Sigh. If you're doing bulk file transfers, you never hit that problem. If you're sending enough data to fill up outgoing buffers, there's no delay. If you send all the data and close the TCP connection, there's no delay after the last packet. If you do send, reply, send, reply, there's no delay. If you do bulk sends, there's no delay. If you do send, send, reply, there's a delay.

```
# 100ms RTT
00.000000 IP 10.0.0.1.47748 > 10.0.0.2.2009: Flags [S], seq 2158272677, win 42340, options [mss 1460,sackOK,TS val 1746926009 ecr 0,nop,wscale 9], length 0
00.100765 IP 10.0.0.2.2009 > 10.0.0.1.47748: Flags [S.], seq 2484846425, ack 2158272678, win 43440, options [mss 1460,sackOK,TS val 4273081757 ecr 1746926009,nop,wscale 9], length 0
00.100823 IP 10.0.0.1.47748 > 10.0.0.2.2009: Flags [.], ack 1, win 83, options [nop,nop,TS val 1746926110 ecr 4273081757], length 0

# Send 2000 bytes, 1 RTT
02.397737 IP 10.0.0.1.47748 > 10.0.0.2.2009: Flags [P.], seq 1:2001, ack 1, win 83, length 2000
02.498120 IP 10.0.0.2.2009 > 10.0.0.1.47748: Flags [.], ack 2001, win 83, length 0

# Send 1000 + 1000 bytes, 2 * RTT
08.085472 IP 10.0.0.1.47748 > 10.0.0.2.2009: Flags [P.], seq 2001:3001, ack 1, win 83, length 1000
08.185750 IP 10.0.0.2.2009 > 10.0.0.1.47748: Flags [.], ack 3001, win 83, length 0
08.185780 IP 10.0.0.1.47748 > 10.0.0.2.2009: Flags [P.], seq 3001:4001, ack 1, win 83, length 1000
08.286105 IP 10.0.0.2.2009 > 10.0.0.1.47748: Flags [.], ack 4001, win 83, length 0

# Set TCP_NODELAY, send 1000 + 1000 bytes
20.869188 IP 10.0.0.1.47748 > 10.0.0.2.2009: Flags [P.], seq 4001:5001, ack 1, win 83, length 1000
20.869312 IP 10.0.0.1.47748 > 10.0.0.2.2009: Flags [P.], seq 5001:6001, ack 1, win 83, length 1000
20.970120 IP 10.0.0.2.2009 > 10.0.0.1.47748: Flags [.], ack 5001, win 83, length 0
20.970169 IP 10.0.0.2.2009 > 10.0.0.1.47748: Flags [.], ack 6001, win 82, length 0

# Bye
22.501407 IP 10.0.0.1.47748 > 10.0.0.2.2009: Flags [F.], seq 6001, ack 1, win 83, length 0
22.601930 IP 10.0.0.2.2009 > 10.0.0.1.47748: Flags [F.], seq 1, ack 6002, win 83, length 0
22.601975 IP 10.0.0.1.47748 > 10.0.0.2.2009: Flags [.], ack 2, win 83, length 0
```
