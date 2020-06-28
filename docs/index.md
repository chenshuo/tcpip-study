# Shuo Chen's notes on Linux TCP/IP stack

## TCP/IP Reference

_TCP/IP Illustrated (vol. 1): The Protocols, 2nd ed._ by Kevin R. Fall and W. Richard Stevens, 2011/11.

* [RFC793](https://tools.ietf.org/html/rfc793) Transmission Control Protocol, 1981/09
* [RFC1122](https://tools.ietf.org/html/rfc1122) Requirements for Internet Hosts --- Communication Layers, 1989/10
* [RFC5681](https://tools.ietf.org/html/rfc5681) TCP Congestion Control, 2009/09
* [RFC6093](https://tools.ietf.org/html/rfc6093) On the Implementation of the TCP Urgent Mechanism, 2011/01,
  which recommends against the use of urgent mechanism.
* [RFC7323](https://tools.ietf.org/html/rfc7323) TCP Extensions for High Performance
* [RFC7413](https://tools.ietf.org/html/rfc7414) TCP Fast Open
* [RFC7414](https://tools.ietf.org/html/rfc7414) TCP Roadmap
* Many others

## TCP/IP Implementations

* BSD family
    * 4.4BSD-Lite2, convered in _TCP/IP Illustrated (vol. 2): The Implementation_ by
      Gary R. Wright and W. Richard Stevens, 1995.
    * FreeBSD
* Linux
    * First in 0.98?
* lwIP / uIP
    * For microcontrollers, small footprint
* gvisor / [netstack](https://github.com/google/gvisor/tree/master/pkg/tcpip)
    * User space, in Golang
* Educational OSes
    * Minix
    * Xinu, covered in _Internetworking With TCP/IP Volume II: Design, Implementation, and Internals, 3rd ed._ by
      [Douglas E. Comer](https://www.cs.purdue.edu/homes/comer/netbooks.html) and David L. Stevens, 1999.

## Tools

* [packetdrill](https://github.com/google/packetdrill) is a unittest for entire TCP/IP stack.
* [neper](https://github.com/google/neper) is a performance testing tool to generate workloads.

## Recent changes

Recent changes that I am aware of.

* **EDT** [netdev](https://netdevconf.info/) [0x12](https://netdevconf.info/0x12/) [Keynote](https://netdevconf.info/0x12/session.html?evolving-from-afap-teaching-nics-about-time):
  Evolving from AFAP: Teaching NICs about time by [Van Jacobson](https://en.wikipedia.org/wiki/Van_Jacobson),
  [slides](https://www.files.netdevconf.info/d/4ee0a09788fe49709855/) and [video](https://youtu.be/MAni0_lN7zE).
    * [Linux 4.20](https://kernelnewbies.org/Linux_4.20#TCP:_switch_to_Early_Departure_Time_model) [switched](https://lwn.net/ml/netdev/20180921155154.49489-1-edumazet@google.com/) to
      Early Departure Time model in 2018/09, and [refined](https://lwn.net/ml/netdev/20181015163758.232436-1-edumazet@google.com/) in 2018/10.


## Historical notes

In 2004, Vint Cerf and Bob Kahn received the Turing Award for their foundational work on TCP/IP.
