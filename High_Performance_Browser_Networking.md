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
	* `Flow Control`
		* Prevent sender from overwhelming the receiver with data it may not be able to process.
		* Each side of connection advertises it own receive window (`rwnd`). It is the size of available buffer space to hold incoming data.
		* Any side can re-advertise the window size if it is not able to keep up.
		* If it is re-advertised as zero, data is no longer sent.
		* Each ACK packet contains the last advertised `rwnd` value.
	* `Slow-start`
		* Flow control prevents the sender from overwhelming the receiver.
		* But there was no mechanism to prevent either side from overwhelming the network.
		* Neither side knows the available bandwidth at the beginning of connection.
		* Also need to adapt to changing conditions of the network.
		* Proposed by Van Jacobson and Michael Karels in 1988 along with `congestion avoidance`, `fast retransmit` and `fast recovery`.
		* Process
			* Server initializes a new congestion window (`cwnd`) variable per TCP connection.
			* It initial value is a conservative system specified value.
			* `cwnd` - Server side limit on the amount of data the sender can have in flight before receiving an ACK.
			* cwnd is not exchanged between server and client.
			* Maximum amount of in flight data is minimum of rwnd and cwnd.
			* As packets are acknowledged, we can increase the window size.
			* Every new connection goes through this phase and hence full bandwidth cannot be utilized immediately.
	* `Congestion Avoidance`
		* TCP uses packet loss as a feedback mechanism.
		* cwnd is increased for every ACK unless
			* Exceeds the receiver's flow control window (`ssthres`). This is a system configured threshold.
			* A packet is lost.
		* At this point congestion avoidance algorithm is trigerred.
			* Congestion windows is reset.
			* Windows size grows but at a lesser rate.
* `Bandwidth Delay Product`
	* Considers the implication of roundtrip time between sender and receiver in determining the congestion window size.
* `Head of Line Blocking`
	* Each TCP packet has a unique sequence number.
	* If one of the packets is dropped, then all subsequent packets are held in receiver's buffer until the lost packet is retransmitted and arrives at receiver.
	* This causes a delay at application layer, which has no visibility of packet loss. This is called `head of line blocking`.
## Building Blocks of UDP
* `UDP`
	* User Datagram Protocol
	* Added by Jon Postel in 1980.
	* RFC 768
	* No delivery guarantees.
	* No failure notifications.
	* No ordering guarantees.
	* Stateless
* `Datagram` - Self contained, independent entity of data, carrying sufficient routing information.
* `Network Address Translators (NAT)`
	* RFC 1631
	* Introduced to resolve IP depletion problem.
	* Proposed to introduce NAT at network edges.
	* Responsible for maintaining a table mapping local IP and port tuples to globally unique public IP and port tuples.
	* Though proposed as a temporary solution, soon they became the de-facto solution.
* Reserved private network ranges
	* 10.0.0.0–10.255.255.255 - 16,777,216 addresses.
	* 172.16.0.0–172.31.255.255 - 1,048,576 addresses.
	* 192.168.0.0–192.168.255.255 - 65,536 addresses.
* Connection-State Timeouts
	* The issue with NAT wrt UDP is that routing table should be maintained.
	* NAT relies on long living connection state. However UDP has no connection state.
	* On the otherhand TCP has a well defined protocol, handshake, connection state and termination mechanism. These work very well with NAT.
	* Outbound UDP is not an issue. However receiving replies requires extra work at NAT. NAT should maintain the routing information for a defined time.
* NAT Traversal
	* NAT imposes other issues with UDP.
		* Client may need to know its public IP if it is being sent as part of UDP payload.
		* NAT device should have a mapping in translation table to map the public port of destination with local port.
	* Solutions proposed
		* `Session Traversal Utilites for NAT (STUN)`
		* `Traversla Using Relays around NAT (TURN)`
		* `Interactive Connectivity Establishment (ICE)`
* `STUN`
	* Allows to discover a NAT on the network.
	* Allows to query the public IP and port tuple for current connection.
	* Application sends a bind request to a STUN server. STUN server replies with a response containing the public IP and port tuple.
	* Support for keepalive pings for long living routing entries.
	* RFC 5389.
* `TURN`
	* Addresses issues in STUN like
		* Not suitable for all network topologies.
		* Firewall blocking.
	* Uses TCP as a fallback.
## Transport Layer Security (TLS)
* `SSL`
	* Originally developed at Netscape.
	* SSL 2.0 was first public release.
	* SSL 3.0 was released later.
* `TLS`
	* Since SSL was proprietary, IETF standardized SSL as TLS.
	* TLS 1.0 was released on January 1999. RFC 2246.
	* TLS 1.1 - April 2006
	* TLS 1.2 - August 2008. RFC 5246.
	* Services offerred
		* Encryption
		* Authentication
		* Integrity
	* Handshake
		* TCP Handshake
		* Client says ClientHello
			* TLS version
			* List of supported cipher suites.
			* Other TLS options.
		* Server responds with ServerHello + Certificate + ServerHelloDone
		* Client says ClientKeyExchange + ChangeCipherSpecFinished
		* Server replies ChangeCipherSpecFinished
		* Application data can be transmitted.
* `Application Layer Protocol Negotiation (ALPN)`
	* Allows application to communicate over TLS and decide on the protocol specific to the application.
	* `ProtocolNameList` in ClientHello message specifies the list of supported protocols.
	* Server responds with the appropriate `ProtocolName` in ServerHello message.
	* IETF adopted version of `Next Protocol Negotiation (NPN)`. NPN was part of SPDY.
* `Server Name Indication (SNI)`
	* Server can host multiple independent sites, each with their own certificates on the same IP port tuple.
	* Client can specify the host he is wanting to connect to as part of ClientHello.
* `TLS Session Resumption`
	* Resume or share the same negotiated secret across multiple connections.
	* `Session Identifiers`
		* RFC 5246
		* Introduced first in SSL 2.0.
		* SessionID is generated by server and sent as part of ServerHello.
		* Client can reuse the SessionID in subsequent ClientHello.
		* Disadvantages
			* Multi site deployments
			* Memory footprint
	* `Session Ticket`
		* RFC 5077
		* Client indicates that it support session tickets.
		* Server includes a `New Session Ticket` record. This contains session specific data encrypted with a secret key.
		* Session ticket is stored by client and sent as part of `Session Ticket` extension in ClientHello.
# References
* [Source](https://hpbn.co/)
