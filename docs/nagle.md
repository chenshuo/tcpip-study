# Nagle's Algorithm

[Nagle's Algorithm](https://en.wikipedia.org/wiki/Nagle%27s_algorithm)

* [RFC896](https://tools.ietf.org/html/rfc896): Congestion Control in IP/TCP Internetworks, John Nagle , 1984-01.
    *  [Minshall's update](https://datatracker.ietf.org/doc/html/draft-minshall-nagle), 1999.


* [The trouble with the Nagle algorithm ](https://developers.slashdot.org/comments.pl?sid=174457&cid=14515105) by John Nagle, 2006.

* [Nginx Optimization: understanding sendfile, tcp_nodelay and tcp_nopush](https://thoughts.t37.net/nginx-optimization-understanding-sendfile-tcp-nodelay-and-tcp-nopush-c55cdd276765)

* In reply to above post, John Nagle on Nagle's algorithm <https://news.ycombinator.com/item?id=9045125>, 2015.

> Sigh. If you're doing bulk file transfers, you never hit that problem. If you're sending enough data to fill up outgoing buffers, there's no delay. If you send all the data and close the TCP connection, there's no delay after the last packet. If you do send, reply, send, reply, there's no delay. If you do bulk sends, there's no delay. If you do send, send, reply, there's a delay.
