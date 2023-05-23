# W. Richard Stevens

[W. Richard Stevens](https://en.wikipedia.org/wiki/W._Richard_Stevens) (1951 ~ 1999) was an extraordinary tech writer and teacher.
He's considered as [Guru of the Unix gurus](https://www.salon.com/2000/09/01/rich_stevens/).

Before he published _Unix Network Programming 1/e_ in 1990, there was very limited resource on how to program with BSD Sockets API.
Besides manpages, I could only found two short tutorials from BSD documents, ~20 pages each.

* [An Introductory 4.3BSD Interprocess Communication Tutorial](http://www.bitsavers.org/pdf/mtXinu/MT_XINU_PS1_Apr_1986.pdf), Stuart Sechrest, 1986.
* [An Advanced 4.3BSD Interprocess Communication Tutorial](http://www2.units.it/mumolo/reti_nettuno/bsdsocket.pdf), Samuel J. Leffler, et al., 1986.

Quote from [Usenix Lifetime Achievement Award](https://www.usenix.org/about/awards/flame):

> He made network programming accessible, even possible, to learn.

> His three volume work, _TCP/IP Illustrated_, was so complete and true to reality
> that it is used as a reference by members of the Internet Engineering Task Force,
> the group charged with creating and maintaining the standards for the Internet.

For example, _TCP/IP Illustrated, vol. 1: The Protocols_ was published in Jan 1994.
It often appeared as the reference of TCP, on many papers and RFCs published after that.

* [TCP Vegas: End to End Congestion Avoidance on a Global Internet](https://sites.cs.ucsb.edu/~almeroth/classes/F05.276/papers/vegas.pdf), [Lawrence Brakmo](http://www.brakmo.org/lawrence/papers.html) and [Larry Peterson](https://www.cs.princeton.edu/~llp/), JSAC 1995.
* [Performance Problems in BSD4.4 TCP](http://www.brakmo.org/lawrence/brakmo95bsdtcp.pdf), L. Brakmo and L. Peterson, 1995.
* [RFC1948](https://datatracker.ietf.org/doc/html/rfc2018): Defending Against Sequence Number Attacks, 1996.
* [Simulation-based Comparisons of Tahoe, Reno, and SACK TCP](https://ee.lbl.gov/papers/sacks.pdf), [Kevin Fall](http://www.kfall.com/) and [Sally Floyd](https://en.wikipedia.org/wiki/Sally_Floyd), 1996.
* [RFC2018](https://datatracker.ietf.org/doc/html/rfc2018): TCP Selective Acknowledgment Options, 1996.
* [Automated Packet Trace Analysis of TCP Implementations](http://conferences.sigcomm.org/sigcomm/1997/papers/p054.pdf), Vern Paxson, 1997.
* [The Macroscopic Behavior of the TCP Congestion Avoidance Algorithm](https://www.cs.utexas.edu/users/lam/395t/papers/Mathis1998.pdf), [Matthew Mathis](https://dblp.org/pid/00/3534.html) et al., 1998.
* [TCP Congestion Control with a Misbehaving Receiver](https://cseweb.ucsd.edu/~savage/papers/CCR99.pdf), [Stefan Savage](https://cseweb.ucsd.edu/~savage/) et al., 1999.
* [RFC2525](https://datatracker.ietf.org/doc/html/rfc2525): Known TCP Implementation Problems, 1999.

Besides the classic books he wrote, W. Stevens authored and co-authored two series of RFCs.

* TCP Congestion Control
    * RFC2001: TCP Slow Start, Congestion Avoidance, Fast Retransmit, and Fast Recovery Algorithms, W. Stevens, 1997
        * First RFC to document TCP congestion control, much of this memo is taken from TCPv1 and TCPv2 books.
    * RFC2581: TCP Congestion Control, M. Allman, V. Paxson, W. Stevens, 1999.

In RFC5681, the current standard of TCP congestion control, in Acknowledgments:

>  W. Richard ("Rich") Stevens wrote the first version of this document
>  [RFC2001] and co-authored the second version [RFC2581].  This present
>  version much benefits from his clarity and thoughtfulness of
>  description, and we are grateful for Rich's contributions in
>  elucidating TCP congestion control, as well as in more broadly
>  helping us understand numerous issues relating to networking.


* IPv6 Sockets API
    * RFC 2133: Basic Socket Interface Extensions for IPv6, Gilligan, R., Thomson, S., Bound, J., and W. Stevens, 1997.
        * RFC 2533: 1999
        * RFC 3493: 2003
    * RFC 2292: Advanced Sockets API for IPv6, W. Stevens and M. Thomas, 1998.
        * RFC 3542: 2003
