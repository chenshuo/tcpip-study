# Links

Literatures I have or haven't read.

* [Sizing Router Buffers - Small is the New Big](https://community.juniper.net/blogs/sharada-yeluri/2023/02/22/sizing-router-buffers)
* [Optimizing TCP for high WAN throughput while preserving low latency](https://blog.cloudflare.com/optimizing-tcp-for-high-throughput-and-low-latency/)
* [Tuning Linux TCP for data-center networks](https://lpc.events/event/16/contributions/1343/attachments/1027/1972/Tuning%20Linux%20TCP%20for%20data-center%20networks%20%283%29.pdf) by Yuchung Cheng,
  Linux Plumbers 2022.
* [Socksdirect: datacenter sockets can be fast and compatible](https://dlnext.acm.org/doi/abs/10.1145/3341302.3342071), SIGCOMM '19.
  [PDF](https://www.microsoft.com/en-us/research/uploads/prod/2019/08/p90-li.pdf)
* [Busypolling next generation](https://netdevconf.info/2.1/session.html?dumazet) by Eric Dumazet, 2017.
* [Making Linux TCP Fast](https://netdevconf.info/1.2/session.html?yuchung-cheng) by Yuchung Cheng and Neal Cardwell, 2016.
  [paper](https://netdevconf.info/1.2/papers/bbr-netdev-1.2.new.new.pdf)
* [TCP Options for Low Latency: Maximum ACK Delay and Microsecond Timestamps](https://www.ietf.org/proceedings/97/slides/slides-97-tcpm-tcp-options-for-low-latency-00.pdf), by Neal Cardwell, Yuchung Cheng and Eric Dumazet, 2016.
* [Kernel Networking Walkthrough](https://www.slideshare.net/ThomasGraf5/linuxcon-2015-linux-kernel-networking-walkthrough) by Thomas Graf (tglx), LinuxCon 2015.
Nice and short (~20 slides) intro to NAPI, RSS, RPS, GRO, TSO, FastOpen with pictures.
    * Next year: [Kernel Networking Explained](https://www.slideshare.net/ThomasGraf5/linux-networking-explained) also by Thomas Graf, LinuxCon 2016, 27 slides.
* [Linux Networking Architecture](https://www.slideshare.net/hugolu/the-linux-networking-architecture/63) slides by Hugo Lu, 2014.
* [Queueing in the Linux Network Stack](https://www.coverfire.com/articles/queueing-in-the-linux-network-stack/), 2013.
* [TCP small queues](https://lwn.net/Articles/507065/), LWN 2012.
* [Controlling Queue Delay](https://queue.acm.org/detail.cfm?id=2209336) by Kathleen Nichols and Van Jacobson, ACM Queue May 2012.
* [EECS 489: Computer Networks at umich.edu](https://www.eecs.umich.edu/courses/eecs489/w10/syllabus.html)
* [Tuning TCP Parameters for the 21st Century](https://www.ietf.org/proceedings/75/slides/tcpm-1.pdf), H.K. Jerry Chu, IETF 75, 2009.
* [You don't know jack about Network Performance](https://queue.acm.org/detail.cfm?id=1066069) by Kevin Fall and Steve McCanne, ACM Queue June 2005.  Clearly explains four types of [network delay](https://en.wikipedia.org/wiki/Network_delay).
* [TCP Implementation in Linux: A Brief Tutorial](http://www.ece.virginia.edu/mv/research/DOE09/publications/TCPlinux.pdf), 2008. Nice two-page overview of TCP/IP stack in Linux 2.6.19.
* [Scaling in the Linux Networking Stack](https://www.kernel.org/doc/Documentation/networking/scaling.txt), kernel doc that describes RSS, RPS, RFS, XPS, etc.
* [Segmentation Offloads in the Linux Networking Stack](https://www.kernel.org/doc/Documentation/networking/segmentation-offloads.txt), about TSO, GSO, GRO, etc.
* [Programming with the Netpoll API](http://people.redhat.com/~jmoyer/netpoll-linux_kongress-2005.pdf) by Jeff Moyer, Linux Kongress 2005.
* [Kernel data flow of 2.6.20](https://wiki.linuxfoundation.org/networking/kernel_flow) ![img](img/Network_data_flow_through_kernel.png)
* [Computer Networks: A Systems Approach 6/e](https://book.systemsapproach.org/) by Larry Peterson and Bruce Davie, 2020.


## RFCs
* [RFC1958](https://tools.ietf.org/html/rfc1958) Architectural Principles of the Internet, 1996-06.
* [RFC2525](https://tools.ietf.org/html/rfc2525) Known TCP Implementation Problems, 1999-03.
* [RFC2544](https://tools.ietf.org/html/rfc2544) Benchmarking Methodology for Network Interconnect Devices, 1999-03.
* [RFC3150](https://tools.ietf.org/html/rfc3150) End-to-end Performance Implications of Slow Links, 2001-07.
* [RFC3439](https://tools.ietf.org/html/rfc3439) Some Internet Architectural Guidelines and Philosophy, 2002-12.
  "Layering Considered Harmful." linked from [Internet protocol suite](https://en.wikipedia.org/wiki/Internet_protocol_suite#Layer_names_and_number_of_layers_in_the_literature)
* [RFC6349](https://www.rfc-editor.org/rfc/rfc6349) Framework for TCP Throughput Testing, 2011-08.
* [RFC8900](https://tools.ietf.org/html/rfc8900) IP Fragmentation Considered Fragile
* [RFC9006](https://tools.ietf.org/html/rfc9006) TCP Usage Guidance in the Internet of Things (IoT), 2021-03.


## RFC drafts
* <https://tools.ietf.org/html/draft-dukkipati-tcpm-tcp-loss-probe-01>, early version of [RFC8985](https://tools.ietf.org/html/rfc8985)?
* <https://tools.ietf.org/html/draft-cardwell-iccrg-bbr-congestion-control-00>


## Congestion Control

* [TCP Congestion Control: A Systems Approach](https://tcpcc.systemsapproach.org/) by Peterson, Brakmo, and Davie.
* [The Great Internet TCP Congestion Control Census](https://www.comp.nus.edu.sg/~ayush/images/sigmetrics2020-gordon.pdf) by Ayush Mishra, et al., SIGMETRICS 2020.
[Slides 1](https://www.comp.nus.edu.sg/~bleong/slides/sigmetrics19-gordon-slides.pdf),
[slides 2](https://datatracker.ietf.org/meeting/109/materials/slides-109-iccrg-the-great-internet-tcp-congestion-control-census-00).
* [The classification and evolution of variants of TCP congestion control](https://www.researchgate.net/figure/The-classification-and-evolution-of-variants-of-TCP-congestion-control-Afanasyev-et-al_fig1_262053709)


## Emulation of lossy link

* <https://mininet.org/>
* [High Speed Network Protocols and Security Workshop](http://ce.sc.edu/cyberinfra/workshop_2020.html), 2020.
    * [Network Tools and Protocols Lab Manual](http://ce.sc.edu/cyberinfra/workshops/Material/NTP/NTP.pdf)
    * [High-Speed Networks: A Tutorial](https://link.springer.com/book/10.1007/978-3-030-88841-1), 2022.
* <https://calomel.org/network_loss_emulation.html>
* <https://spl0dge.wordpress.com/2013/09/08/building-a-wan-simulator/>
* <https://web.archive.org/web/20160306040049/http://tdistler.com/2011/06/10/netem-wan-emulation-how-to-setup-a-netem-box> set up a bridge and introduce packet loss and delay.


## Posts

* [Segmentation and Checksum Offloading: Turning Off with ethtool](https://sandilands.info/sgordon/segmentation-offloading-with-wireshark-and-ethtool) by Dr Steven Gordon, 2010
* [Reply from David Miller](https://seclists.org/tcpdump/2009/q3/14) about capturing packets when GSO is on.
* [Coping with the TCP TIME-WAIT state on busy Linux servers](https://vincent.bernat.ch/en/blog/2014-tcp-time-wait-state-linux)
* [Harping on ARP](https://lwn.net/Articles/45373/), [Multiple Interfaces on Same Ethernet Broadcast Network](https://www.kernel.org/doc/html/v4.18/networking/e100.html#multiple-interfaces-on-same-ethernet-broadcast-network)
* [Increase HTTP Performance by Fitting In the Initial TCP Slow Start Window](https://sirupsen.com/napkin/problem-15)
* [Experimenting with TCP Congestion control](https://dipsingh.github.io/TCP-Congestion-Experiment/)
