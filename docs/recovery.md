# TCP loss recovery

There are a number of ways to recovery from packet (segment) loss, here's a list that I am aware of.

* RTO, since 1981 (RFC793), most basic, catch-all.
* Fast retransmit, since 1988. 3 duplicated ACKS.
* SACK
* FACK
* Early retransmit
* RACK and TLP

