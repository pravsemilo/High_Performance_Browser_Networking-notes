# Networking 101
## Primer on Latency and Bandwidth
* `Latency`
* `Bandwidth`
* Components of latency
  * Propogation delay
  * Transmission delay
  * Processing delay
  * Queuing delay
* `Bufferbloat`
  * Coined by Jim Gettys in 2010.
  * [ACM Queue article](http://queue.acm.org/detail.cfm?id=2209336)
## Buidling Blocks of TCP
* `IP`
  * Routing
  * Addressing
* `TCP`
  * Provides abstraction of a reliable network over an unreliable channel.
  * Retransmission
  * In-order delivery
  * Congestion control
  * Congestion avoidance
  * Data integrity
* `TCP/IP`
  * Referred as Internet Protocol Suite.
  * Proposed by Vint Cerf and Bob Kahn in 1974 paper titled "A Protocol For Packet Network Intercommunication".
  * The original proposal (RFC 675) was revised several times, and in 1981 the v4 specification of TCP/IP was published not as one, but as two separate RFCs:
    * RFC 791 — Internet Protocol
    * RFC 793 — Transmission Control Protocol
* We are all familiar with IPv4 and IPv6, but what happened to IPv{1,2,3,5}? The 4 in IPv4 stands for the version 4 of the TCP/IP protocol, which was published in September 1981. The original TCP/IP proposal coupled the two protocols, and it was the v4 draft that officially split the two into separate RFCs. Hence, the v4 in IPv4 is a heritage of its relationship to TCP: there are no standalone IPv1, IPv2, or IPv3 protocols. When the working group began work on "Internet Protocol next generation" (IPng) in 1994, a new version number was needed, but v5 was already assigned to another experimental protocol: Internet Stream Protocol (ST). As it turns out, ST never took off, which is why few ever heard of it. Hence the 6 in IPv6.
* `Three-way Handshake`
  * All connections begin with TCP handshake.
  * Before changing any application data, agreement should be done on packet sequence numbers, connection specific variables etc.
  * `SYN` - Client picks a random sequence number x and sends a SYN packet, which may also include additional TCP flags and options.
  * `SYN ACK` - Server increments x by one, picks own random sequence number y, appends its own set of flags and options, and dispatches the response.
  * `ACK` - Client increments both x and y by one and completes the handshake by dispatching the last ACK packet in the handshake.
  * Each new connetion will have a full roundtrip of latency before any data can be transferred.
  * Connection reuse is critical.
* `TCP Fast Open`
  * Allows to send data in SYN packet.
  * Limit on the data being sent.
* `Congestion Avoidance and Control`
  * In 1984, John Nagle documented a condition known as "congestion collapse".
# References
* [Source](https://hpbn.co/)