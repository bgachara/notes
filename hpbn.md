# High Performance Browser Networking

ref:`High Performance Browser Networking`
    `HTTP: The Definitive Guide`
    

## Networking 101

### Primer on Latency and Bandwidth

- Speed is a feature.
- Latency
  - the time from source sending a packet to destination receiving it.
- Bandwidth
  - maximum throughput of a logical or physical communication path.
  - size of cable.
- Components of Latency
  - propagation delay
    - amount of time required for a message to travel from sender to receiver, which is a function of distance over speed with which the signal propagates.
  - transmission delay
    - time required to push all the packet's bits into the lin, function of packet's length and data rate of the link.
  - processing delay
    - time required to process packet header, check for bit-level errors, and determine packet's destination
  - queuing delay
    - time incoming packet is waiting in the queue until it can be processed.
    - normally inside an incoming buffer.
- Total latency is sum of all above delays.
- The speed of light is the maximum speed at which all energy, matter and information can travel.
- Spped of light - 299,792, 458 metres per second or 186,282 miles per second. In a vacuum though.
- Refractive index of material - Ratio of speed of light and speed at which light travels in a given material. The larger it is, the slower light travels.
- Fiber optics is 1.4 to 1.6.
- Assume speed is 200m per second
- Users report lag when 100ms-200ms delay is introduced.
- CDNs compensate speed with proximity.
- Last-mile latency
- Investigate
  - traceroute
  - mtr - my traceroute
  - tracert - Ms
- Fiber optics preferred as each fible can cary varying wavelengths of light via a process known as wavelength-division multiplexing(WDM)
- Total wavelength bandwidth of a fiber link is the multiple of per-channel data rate and the number of multiplexed channels.
- As of early 2010, researchers able to multiplex over 400 wavelengths with peak capacity of 171gbit/s per channel for a peak bandwidth of 70Tbit/s for single fiber link.
- Each cable carries several strands of fiber(4 common number) which translates to bandwidth of hundreds of terabits per second for each cable.
- Available bandwidth to the user is a function of the lowest capacity link between the client and destination server.


### Building Blocks of TCP.

- Heart of Internet are two protocols, TCP and IP.
- Internet Protocol - provides the host-to-host routing and addressing
- Transmission Control Protocol - provides the abstraction of a reliable network running over an unreliable channel.
- ref paper:`a protocol for packet networking communication`.
- RFC 791 - Internet Protocol
- RFC 793 - Transmission Control Protocol.
- provides an effective abstraction of a reliable network running over an unreliable channel, hiding most of the complexity of network communication from our
  apps via retransmission of data, in-order delivery, congestion control, avoidance, data integrity and more.
- TCP stream - guaranteed that all bytes sent will be identical with bytes received and in order.
- Optimised for accurate delivery rather than a timely one.
- This creates some challenges when optimising for browser performance 
- HTTP standard does not specify TCP as the only transport protocol, it's just preffered due to its many great features.

#### Three-Way Handshake

- All TCP conenctions start with a three way handshake.
- Before the client and server can exchange any application data, they must agree on starting packet sequence numbers as well as other connection specific variables.
- Sequence numbers are picked randomly for security reasons
- Desc
  - SYN
    - Client picks a random sequence number X and sends a SYN packet, which may also include additional TCP flags and options.
  - SYN ACK
    - Server increments x by one, picks own random number y, appends its own set of flags and options and dispatches the response.
  - ACK
    - Client increments both x and y by one and completes the handshake by dispatching the last ACK packet in the handshake.
- The delay imposed by the three-way handshake makes new TCP connections expensive to create and is the reason why connection reuse is critical optimization for any app running over TCP.
- TCP Fast Open (TFO) - mechanism that aims to reduce the latency penalty imposed on new TCP connections
- availabel in linux kernels 3.7+
- Congestion avoidance and control
  - Congestion collapse 
    - caused by interaction between transport and datagram layers.
  
  - Flow control
    - mechanism to prevent the sender from overwhelming the receiver with data it not be able to process, may be busy, overloaded or only has certain amout of buffer space.
    - each side of the connection advertises its own receive window(rwnd), which communicates the size of available buffer space to hold the incoming data.
    - on connection is first established, both sides initiate their rwnd values by using system default settings.
    - if one of the sides is not able to keep up, it can advertise a smaller window to the sender, with 0 meaning no more data should be sent.
    - workflow contains via the entire lifetime of the connection.
    - each ACK packet carries the latest rwnd value for each side, allowing both sides to dynamically adjust the data flow rate to the capacity and processing speed of the sender and the receiver.
    - TCP scaling window from 2^16 to 1G, communicated during 3-way handshake
  
  - Slow start
    - flow control prevented either side from overwhelming each other but not the network itself as none could know capacity of it.
    - 1988, Jacobson and Karels documented, slow-start, congestion avoidance, fast retransmit, fast recovery.
    - only way to estimate available capacity is to measure it by exchanging data.
    - to start server initialises anew congestion window variable per TCP connection and sets its initial value to a conservative, system-specified value(initcwnd on linux).
    - cwnd - sender-side limit on the amount of data the sender can have in flight before acknowledge from the client.
    - cwnd is not advertised or exchanged between the sender and the receiver, private variable maintained on server.
    - new rule introduced, the minumum data in flight is the minimum of the cwnd and rwnd variables.
    - how does the client and server determine optimal values for their congestion window sizes??
    - Soln: start slow and grow the window size as the packets are acknowledged: slow start.
    - on connection it is 4, hence can only send those before ack to send more.
    - Then for every received ACK, the slow-start algorithm indicates that the server can increment its cwnd window segment by one segment.
    - For every ACKed packet, two new packets can be sent.
    - This is the exponential growth as client and server are quickly trying to converge on available bandwidth on the network path between them.
    - To decrease time taken to grow the congestion window, we can decrease the roundtrip time between client and server...move closer or increase initial congestion window size to new RFC 6928 vlaue of 10 segments
    - Not an issue for large, streaming downloads as client and server will arrive at max window after a few milliseconds and amortise over lifetime of transfer
    - However many HTTP connections are bursty and hence terminate before max window size is reached.
    - Slow-start restart ... resets the congestion window of a connection after being idle, assumption being network might have changed.
    - Adviced to disable SSR on the server as it can significant impact on long-lived TCP connections that may be idle for bursts of time.
  
  - Congestion Avoidance
    - recognize TCP is specifically designed to use packet loss as a feedback mechanism to help regulate its performance.
    - slow-start initializes and runs it exceeds the receiver's flow-control window, a system-configured congestion threshold(ssthresh) window or until a packet is lost at which point the congestion avoidance algorithm takes over.
    - implicit assumption in c.a is a packet has been lost due to network congestion and hence we need to adjust our window to avoid inducing more packet loss to avoid overwhelming the network.
    - Once the congestion window is reset, C.A specifies its own algorithms for how to grow the window to minimize further loss.
    - At a certain point, another packet loss event will occur and the process will repeat over again. 
    - This explains the throughput trace of a TCP connection having a sawtooth pattern within it, it is the congestion control and avoidance algorithms adjsuting the congestion window size to account for packet loss in the network.
    - Different adaptations for different network types, different types of data transfers and so on.
    - Determining the optimal way to recover from packet loss is a nontrivial exercise: too aggressive - onepacket could affect whole connection, too soft: more packet loss is guaranteed.
    - Originally used Multiplicative Decrease and Additive Increase(AIMD) - too conservative, when a packet loss occurs, halve the congestion window size and then slowly increase the window by a fixed amount per roundtrip
    - Proportional Rate Reduction - default in linux3.2+ kernels.
  
  - Bandwidth-Delay Product
    - product of data link's capacity and its end-to-end delay. The result is the maximum amount of acknowledged data that can be in flight at any point in time.
    - is a function of the roundtrip time and target data rate
    - performnce implication of built-in congestion control and congestion avoidance mechanisms in TCP is that the optimal sender and receiver window sizes must vary based on the roundtrip time and target data rate between them. 
    - the current receive windows are communicated in every ACK and the congestion window is dynamically adjusted by the sender based on the congestion control and avoidance algorithms
    - if wither of them exceeds it,it must wait till those sent are ACK before proceeding, how long to wait?? dictated by roundtrip time between the two.
    - if either are forced to stop and wait for ACKs for previous packets , this would create gaps in the data flow limiting maximum throughput of the connection.
    - to address this problem, the window sizes should be made just big enough such that either side can continue sending data until an ACK arrives back from client for an earlier packet no gaps, maximum throughput.
    - To achieve higher throughput we need to raise window size or lower the roundtrip time.
    - We can compute optimal window size if we know the roundtrip time and available bandwidth on both ends.
    - small window sizes, saturated peer advertising a lower receive window, bad network weather and high packet loss resetting the congestion window or explicit traffic shaping applied to limit throughput of connection.
  
  - Head-of-line blocking 
    - TCP provides basic packet error checking and correction, in-order delivery, retransmission of lost packets, flow control, congestion control and congestion avoidance.
    - Not all features necessary for every occasion such as in-order and reliable packet delivery which introduce unnecessary delays and negative performnce implications.
    - Using the sequnce numbers on packets, if one packet is lost then all subsequent must be held in TCP buffer until its retransmitted.
    - Application has no view into this both retransmissions or buffered ones so must wait for full sequence before being able to access the data, only sees a delivery delay when it tries to read the data from the socket.
    - This is TCP head-of-line blocking
    - Delay imposed helps our apps not deal with packet reordering and reassembly which makes our code much simpler, at the cost of application being "jittery".
    - Audio, video and game states can tolerate packet losses.
    
  - Optimizing for TCP
    - best way to optimize TCP is to tune how TCP senses the current network conditions and adapts its behaviour based on the type and requirements of the layers below and above it.
    - All described above and more including:
      - Selective Acknowledgements(SACK)
      - Delayed Acknowledgements
      - Fast retransmit
    - Varying application requirements and many knobs in every TCP algorithm make TCP tuning and optimization and inexhaustible area of research.
    - Core principles and implications remain unchanged
      - TCP three-way handshake introduces a full roundtrip of latency.
      - TCP slow-start is applied to every new connection
      - TCP flow and congestion control regulate throughput of all connections
      - TCP throughput is regulated by current window size.
  
  - Tuning Server configuration
    - most of changes are only available in the latest kernels so upgrade hosts to their latest system versions.
    - increase TCP initial congestion window.
    - slow-start restart
    - window scaling - increase maximum receive window size and allows high-latency connections to achieve better throughput
    - TCP fast open - allows data to be sent in initial SYN packet in certain conditions.
    - Use `ss command to check stats for open sockets`
  
  - Tuning Application Behaviour
    - No bit is faster that one that is not sent, send less bits
    - We cant make the bits travel faster, we can move them closer
    - TCP connection reuse is critical to improve performance.
    
  - Performance Checklist
    - upgrade server kernel to latest version
    - ensure cwnd size is set to 10.
    - Disable slow-startafter idle.
    - Ensure window scaling is enabled
    - Eliminate redundant data transfers
    - Compress transferred data.
    - Position servers closer to the users to reduce roundtrip times.
    - Reuse established TCP connections whenever possible. 

## Building Blocks of UDP
  
  - User Datagram Protocol
  - Datagram
    - a self-contained, independent entity of data carrying sufficient information to be routed from the source to the destination nodes without reliance on earlier exchanges between
      the nodes and the transporting network.
  - protocol added in 1980 after introduction of TCP/IP but right as they were being split.
  - primary feature and appeal of UDP is not in what it introduces but rather in all the features it chooses to omit.
  - known as null protocol.
  - packet and datagram can almost be substituted but have some nuances to them in that datagram goes with unreliable service, no delivery guarantee, no failure notifications.
  - Domain Name Service(DNS) relies on UDP.
  - WebRTC standards enable real-time communication such as voice and video calling and other forms of peer-to-peer(P2P) communication natively within the browser via UDP.

### Null Protocol Service

  - First look at IP layer found oen layer below both TCP and UDP protocols.
  - IP layer has the primary task of delivering datagrams from the source to the destination host based on their address.
  - To do so, they are encapsulated within an IP packet with address and other routing parameters.
  - IP layer provides no guarantees about message delivery or notifications of failure and hence directly exposes unreliability of underlying network to the layer above it.
  - If a routing node drops an IP packet for whatever reasons, congestion, heavy load its the responsibility of a protocol above it to detect it, recover it and retransmit the data.
  - IPv4 header(20 bytes)
    - 0 - 32: version, header length, dscp, ecn, total length
    - 32 - 64: identification, flags, fragment offset
    - 64 - 96: time-to-live, protocol, heade chechsum
    - 96 - 128: source IP address
    - 128 - 160: destination IP address
    - 160: options(if present)
  - UDP protocol encapsulates user messages into its own packet structure which adds only four additional fields, source port, destination port, length of packet and checksum.
  - When Ip packet is delivered, host is able to unwrap packet to identify target application by destination port and deliver the message.
  - UDP header(8 bytes)
    - 0: source port, destination port
    - 32: length, checksum
    - payload.
  - Both source ports and checksum fields are optional fields in UDP datagrams.
  - IP contains its own header checksum and the application can choose to omit the UDP checksum, means all error detection and error correction can be delegated to applications above them.
  - At its core, UDP simply provides "application multiplexing" on top of IP by embedding source and target applications ports of the communicating hosts.
  - Summarize all the non-UDP service
    - No guarantee of message delivery
      - no acknowledgements, retransmissions or timeouts
    - No guarantee of order of delivery
      - no packet sequence numbers , no reordering, no head-of-line blocking
    - No connection state tracking
      - no connection establishment or teardown machines.
    - No congestion control          
      - no built-in client or network feedback mechanisms.
  - UDP is a simple, stateless protocol suitable for bootstrapping other application protocols on top, virtually all the protocol design decisions are left to the application above it.
  
### UDP and NAT
  
  - NAT - Network Address Translators.
  - IPv4 is 32 bits long giving 4.29 billion unique addresses.
  - IP NAT spec was introduced to resolve looming ipv4 address depletion problem
  - Introduce NAT devices at the edge of the network each of which would be responsible for maintaining a table mapping of local IP and port tuples to one or more globally unique IP and port tuples.
  - The local IP address space behind the translator could then be reused among many different networks thus solving the address depletion problem.
  - *Nothing more permanent like a temporary solution* NATs
  - NAT middleboxes have gone to become an integral part of the internet infra'.
  - Reserved Private Network Ranges
    - IANA reserved three well known ranges for private networks most often residing behind a NAT.
      - 10.0.0.0 - 10.255.255.255 = 16,777,216
      - 172.16.0.0 - 172.31.255.255 = 1,048,576
      - 192.168.0.0 - 192.168.255.255 = 65,536.
      - To avoid routing errors and confusion, no public computer is allowed to be assigned an IP address from any of these reserved private network ranges.
  
### Connection-state Timeouts
  
  - The issue with NAT translation, at least as far as UDP is concerned is precisely the routing table that it must maintain to deliver the data.
  - NAT middleboxes rely on connection state, whereas UDP has none, this is a fundamental mismatch and a source of many problems for delivering UDP datagrams.
  - Further its not uncommon to have clients behind many layers of NATs, complicated matters further.
  - Each TCPconnection has a well-defined protocol state machine, which begins by handshake, followed by application data transfer and a well defined exchange to close the connection.
  - Given this flow, each middlebox can observe state of the connection and create and remove the routing entries as needed.
  - Wiht UDP there is no handshake or connection termination and hence no connection state machine to monitor.
  - Outbound UDP does not require extra work but routing a reply requires we have an entry in translation table that will tell us IP and port of the local destination host.
  - Translators thus have to keep state about UDP flow which itself is stateless.
  - Translator is also tasked with figuring out when to drop the translation record, but since UDP has no connection termination sequence, each peer could just stop transmitting datagrams at any point without notice.
  - To address this UDP routing records are expired on a timer. Not definitive. Varying implementations
  - Best practices for long-running sessions over UDPis introduce bidirectional keepalive packets to periodically reset the timers for the translation records in all NAT devices along the path.
  - There is no need for TCP connection timeouts on NAT devices, TCP follows a well-defined handashake and termination sequence which signals when the appropriate records can be added or removed.
  - Some NAT devices though apply both kind of timeouts to TCP and UDP connections leading to alternate drops.

### NAT traversals.

  - Unpredictable connections state handling is a serious issue created by NATs but a bigger problem is applications inability to establish UDP connections at all.
  - Issue:
    - in the presence of NAT, internal client is unaware of its public address, it knows its internal IP address and the NAT devices perform the rewriting of the source port and address in every UDP packet as well as originating the IP address within the IP packet.
    - if client comunicates its private IP address as part of its application data with a peer outside of its private network then connection will inevitably fail.
    - hence promise of transparent translation is no longer true and application must first discover its public IP address if its needs to share it with a peer outside its private network.
    - Knowing the public IP is also not sufficient to successfully transmit with UDP.
    - Any packet that arrives at thr public IP of a NAT device must also have a destination port and an entry in the NAT table that can translate it to an internal destination host IP and port tuple.
    - If this entry does not exist, which is the most likely case if someone simply tries to transmit data from the public network, then the packet is simply dropped.
    - NAT acts as a simple packet filter since it has no way to automatically to determine the internal route, unless explicitly configured by the user through a port forwarding or similar mechanism.
    - Server affected mainly by preceding issue as opposed to clients as inbound connections.
    
### STUN, TURN, ICE.

  - Session Traversals Utilities for NAT.
  - protocol that allows host application to discover the presence of a network address translator and when present obtain the allocated public IP and port tuple for the current connection.
  - to do so, requires assistance from a well-known third party STUN server that must reside on the public network.
  - assuming IP address of STUN server is known(DNS discovery or manually specified address), first send a binding request to STUN server.
  - STUN responds with public IP address and port of client as seen from the public network.
  - Workflos addresses all issues form above via:
    - App discovers its public address and port tuple and is then able to use this information as part of its application data when communicating with its peers.
    - Outbound binding request to the STUN server establishes NAT routing entries along the path such that inbound packets can find their way to host application on the internal network.
    - STUN protocol defines a simple mechanism for keepalive pings to keep the NAT routing entries from timing out.
  - Workflow allows two peers to send binding requests to their respective STUN servers and following a successful response on both sides, use established public IP and port tuples to exchange data.
  - STUN not effective for all network topologies and configs, plus firewals may block UDP.
  - TURN, Traversal Using Relays around NAT used as a fallback, which can run over UDP and switch to TCP if all else fails.
  - TURN relies on the presence and availability of a public relay to shuttle data between the peers.
    - both clients begin their connections by sending an allocate reuquest to the same TURN server, followed by permission negotiation     
    - both peers communicate by sending data to the TURN server which relays it to other peers.
  - Obvious downside is its no longer peer-to-peer, TURN operation very high. Last resort.
  - Google libjingle
  - Interactive Connectivity Establishment
    - protocol and a set of methods that seek to establish the most efficient tunnel between the participants, direct, STUN or TURN.
    
### Optimizing for UDP.

  - simple protocol used to bootstrap new transport protocols.
  - primary feature is all the features it omits.
  - flexibility is also its biggest liability.
  - RFC 5405
  - Design guidelines for applications using UDP
    - Apps must tolerate a wide range of internet path conditions.
    - Apps should control rate of transmission
    - App should perform congestion control all over the network.
    - App should use bandwidth similar to TCP.
    - App should back off retransmission counters after loss.
    - App should not send datagrams that exceed path MTU
    - App should handle datagram loss, duplication and reassembly
    - App should be robust to deliverydelays of upto 2mins
    - App should enable IPv4 UDP checksum and must enable IPv6 checksum.
    - App may use keepalive when needed(15secs). 
  -  Enter *webRTC* framework.
  
## Transport Layer Security

- SSL protocol was implemented at the application layer, directly on top of TCP enabling protocols above it(HTTP, email, instant messaging and others) to operate unchanged while
  providing communication security when communicating across the network.
- When used correctly a third-party can infer connection endpoints, type of encryption, as well as the frequency and an approximate amount of data sent, but cannot read or modify any of the actual data. 
- Application - Session - Transport - Network - Data Link - Physical
- When SSL protocol was standardized by IETF it was renamed to TLS...used interchangebly but refer to different protocol implementations.
- TLS 1.0 because SSL belonged to Netscape
- TLS was designed to operate on top of a reliable transport protocol such as TCP.However it has also been adapted to run over datagram protocols such as UDP.
- The Datagram Transport Layer Security(DTLS) protocol,defined in RFC 6347, is based on TLS protocol and is able to provide similar security guarantees.

### Encryption, Authentication and Integrity

- TLS designed to provide all the above services to apps running above it.
- Encryption
  - A mechanism to obfuscate what is sent from one computer to another
- Authentication
  - A mechanism to verify the validity of provided identification material
- Integrity
  - A mechanism to detect message tampering and forgery.
- In order to establish cryptographically secure data channel, connection peers must agree on which ciphersuites will be used and the keys used to encrypt the data.
- The ingenious part of this handshake and the reason TLS works in practice, is its use of public key cryptography(assymetric) which allows parties to negotiate a shared secret key without having to establish any prior knowledge of each other and to do so over an unencrypted channel.
- Protocol also enables both connection peers to authenticate their identity, when used in browser allows client to verify that server is who it claims it is.
- Verification is based on established chain of trust.
- Server can also verify identify of client.
- With encryption and authentication in place, TLS also provides its own framing mechanism and signs each message with message authentication code(MAC)
- MAC is a one-way cryptographic hash function (checksum) the kkeys to which are negotiated by both connection peers.
- When TLS record is sent, MAC address is attached which receiver decrypts to verify message authenticity and integrity.
- Proxies, Intermediaries, TLS  and New Protocols on the Web.

### TLS Handshake

- Before client and server can begin exchanging app data over TLS, encryption tunnel must be negotiated: agree on protocol, ciphersuites, verify certs.
- Each step requires new packet roundtrips adding startup latency to all TLS connections.
- TLS Handshake process
  - TLS runs over a reliable transport (TCP) which means we must first complete the TCP three-way handshake, one full roundtrip.
  - with TCP conn in place, client sends a number of specs in plain text such as TLS protocol its running, supported ciphersuites and other TLS options it may want to use.
  - Server picks TLS protocol for further comm, decided ciphersuite, attached its certificate and sends response back, can also request for client's cert and parameters for other TLS extensions.
  - On successful negotiation of common version and cipher and certificate, client then generates a new symmetric key, encrypts it with servers public key and tells server to switch to new comms.
  - All data upto now has been in plaintext with exception of new symmetric key thats encrypted with the server's public key.
  - Server decrypts the symmetric key sent by the client, checks message integrity by verifying the MAC and returns an encrypted "finished" message to the client.
  - Client decrypts message with the symmetric  key it generated  earlier, verifies the MAC and if all is well, tunnel is established and application data can be sent.
- New TLS connections require 2 roundtrips for a full handshake...existence of half-handshake.
- Given certificates are provided and configured then browser and server do all the rest.
- TLS is two handshakes above TCP's one.
- Performance of Public vs Symmetric Key Cryptography
  - public-key cryptography is used during session setup of the TLS tunnel
    - server provides its public key to the client, client generates symmetric key which it ecrypt with server's public key and returns the encrypted symmetric key to server.
    - server can decrypt with its private key.
  - symmetric key cryptography which uses shared secret key generated by client is then used for all further communication
    - Done in large part to improve performance.
    - public-key crypto is much more computationally expensive.
  - Can run tests
    - openssl speed rsa
    - openssl speed aes

### Application Layer Protocol Negotiation (ALPN)

- Network peers may want to use custom protocols to communicate: as opposed to 80 for HTTP and 443 for TLS 
- TLS extension that introduces support for application protocol negotiatio into the TLS handshake eliminating need for extra roundtrip required by HTTP upgrade
- Client appends a new ProtocolNameList field containing list of  supported application protocols
- Server inspects that field and returns a ProtocolName field indicating selected protocol as part of ServerHello message.
- Abort connection if does not support any of the protocols listed.
- NPN Aand ALPN, server and client first

### Server Name Indication

- What if server wants to host multiple independent sites, each with its own TLS certificate, on the same IP address??
- SNI extension was introduced to TLS protocol, allows client to indicate the hostname its attempting to connect to at start of handshake.
- Web server can inspect SNI hostname, select certificate and continue handshake.

### TLS Session resumption

- Extra latency and computational costs of TLS impose serious performance penalty on all apps that require secure connections.
- To mitigate this TLS provides mechanism to resume or share negotiated secret key data between multiple connections.

#### Session Identifiers

- Introduced in SSL 2.0, which allowed a server to create and send a 32-byte session identifier as part of its ServerHello message.
- Internally server could maintain a cache of sessions IDs and negotiated session parameters for each peer.
- In turn client could also store session ID information and include the ID in CLientHello for subsequent sessions which served as indication of memory of params and can reuse them.
- If both can find their shared session IDparameters in respective caches then abbreviated handshake can take place.
- Leveraging session identifiers eliminates round trips and computational costs of public key cryptography.
- In practice, most web apps attempt to establish multiple connection to the same host to fetch resources in parallel, making session resumption a must-have optimization.
- most browsers wait for the first connection to complete so they can reuse SSL session parameters.
- Practical limitation of this is that server has to create and maintain a session cache for every client...esp for servers with millions of connections, memory consumption, cache and eviction policies and nontrivial deployment challenges.

#### Session Tickets

- Introduced, removing the need for the server to maintain per client session state.
- Instead if client indicated that it supports Session tickets in the last TLS handshake, the server can include a New Session Ticket record which includes all of the session data encrypted with a secret key known only by the server.
- This session ticket is then stored by the client and can be included in the SessionTicket extension within the ClientHello message of a subsequent session.
- Thus all session data is stored only on the client, but ticket is still safe because it is encrypted with a key known only by a server.

- Session identifiers and session tickets are commonly known as session caching and stateless resumption mechanisms.
- Session tickets improvemnts is the removal of server-side session cache, simplifying deployment.

## Chain of Trust and Certificate Authorities

- Authentication is an integral part of establishing every TCP connection.
- Unless one can verify identity of the one communicating with then all encryption work can be for nought.
- Introduce concept of chain of trust.
- Whom does your browser trust and whom do you trust when you use the browser.
- Answers:
  - Manually specified certificates
    - every browser and operating system provides a mechanism for you to manually import any certificate you trust.
    - how you obtain and verify its integrity is completely up to you.
  - Certificate Authorities
    - A CA is a trusted third party that is trusted by both the subject(owner) and party relying upon the certificate.
  - The browser and the operating system.
    - every operating system and browser ship with a list of well-knonw certificate authorities, thus trust software vendor.
- Certificate Authorities are the most common solution.
- Browser specifies which CA to use(root CA) and the burden is then on them to verify each site they sign and to audit and verify against misuse and compromise.
- Every browser allows you to inspect the chain of trust of your connection by clicking on lock icon.
- Site cert - Intermediate cert - Root CA cert
- Operating system and browsers provide public list of CAs that they trust by default.
- As many as they are they do create a large surface attack for intruders.

### Certificate Revocation

- Can happen to cert compromise, authority itself compromised.
- Certs themselves contain instructions on how to check if they have been revoked.
- Chain of trust is then erliant on checking those instructions as it walks up the certificate chain.

### Ceritificate Revocation List

- specifies a simple mechanism to check status of every certificate, each CA maintains and periodically published a list of revoked cert serial numbers.
- CRL file itself can be published periodically or on every update and can be delivered via HTTP or any other file protocol.
- List is signed by CA and allowed to be cached for a specified interval.
- Works well until it doesnt i.e
  - CRL list grow a bit longer than necessary.
  - No mechanism for instant notification of ceritificate revocation so depending on cache status, certificate may or may not be allowed.
- Enter:

### Online Certificate Status Protocol(OCSP)

- provides a mechanism to perform a real-time check on the status of the certificate.
- Allows CA to query the serial number of the certificate in question while validating the certificate chain.
- OCSP should consume much less bandwidth and is able to provide real-time validation.
- This also comes with its issues:
  - CA handle load of queries.
  - Service is up and gloablly available at all times.
  - Client blocks on OCSP requests before navigation proceed.
  - Real-time OCSP impairs client's privacy as CA knows which sites they are visiting.
  
- In practice CRL and OCSP are complementary and most certs will provide instructions and endpoints for both.

## TLS Record Protocol

- all data exchanged within a TLS session is also framed using a well-defined protocol.
- The record protocol is responsible for identifying different types of messages(handshakes, alert or data) via content type field as well as verifying and securing each message.
- Typical workflowfor delivering application data is as follows:
  - Record protocol receives application data.
  - Received data is divided into blocks: max 2^14 bytes or 16KB per record.
  - App data is optionally compressed.
  - Message Authentication Code is added.
  - Data encrypted using negotiated cipher.
  - Data is then passed down to the TCP layer for transport.
  - On receiving end, same process but in reverse.
- All work above is handles by the TLS layer and transparent to most applications.
- Record protocol has a few implications namely:
  - Max TLS record size is 16KB.
  - Each block contains a 5-byte header, a MAC(20bytes for SSL, TLS1 and 32bytes for TLS 1.2) and a padding is block cipher is used.
  - To decrypt and verify the record, the entire record must be available.
- Picking the right record size for your application if you can is an important optimization.
- Small records incur a larger overhead due to record framing whereas large records will have to be delivered and reassembled by the TCP layer before they can be processed by the TLS layer and delivered to the application.

### Optimizing for TLS

- Optimize for TCP first.
- Should investigate operational pieces of your TLS deployments:
  - how and where you deploy your servers
  - size of TLS records and memory buffers
  - certificate sizes
  - support for abbreviated handhshakes.
- Getting above right can not only improve application user experience but also improve operational costs.

#### Computational costs

- establishing and maintaining an encrypted channel introduces additional computational costs for both peers.
- modern h/w improvements minimized those costs, CPU can now handle such calculations.
- FB and Google do it at scale.
- SSL/TLS is not computationally expensive anymore.
- Upgrade your SSL libraries to the latest release and build your web server or proxy against them.

#### Early Termination

- connection setup latency imposed on every TLS connection, new or resumed is an important area of optimization.
- The higher the latency the worser the penalty
- simple technique of placing the serves closer to your users to minimize latency cost of each roundtrip between client and the server.
- Simplest way to do this is replicate or cache your data and services around the world.
- Nearby server can also terminate TLS session reducing TCP and TLS roundtrips and connection latencies 
- Same nearby server can also establish a pool oflong-lived, secure connections tothe origin servers and proxy all incoming requests and responses to and from the origin servers.
- One can also: spin up cloud servers in a few data centers around the globe, configure a proxy server on each to forward requests to your origin, add geographic DNS load balancing.
- Uncached Origin Fetch
  - use a CDN or a proxy server to fetch a resource which may need to be customized per user or contains other private data and hence is not a globally cacheable resource at the edge.
  - CDN maintains a warm connection pool to relay data to the origin  servers.

#### Session Caching and Stateless Resumption

- No bit is faster than a bit not sent.
- enabling this will eliminate an entire roundtrip for repeat visitors.
- Do not assume session support will be on by default.
- Size of shared session cache should be tuned to traffic levels.
- Enable both

#### TLS Record size

- each record size is 16KB.
- 60-100bytes of overhead for each record.
- Small records incur overhead, large records incur latency
- Each TCP packet should carry at least one TLS record and it should occupy maximum segment size allocated.
- Don't use TLS records that span multiple TCP packets
- Check server documentation on how to adjust and configure this setting.
- For optimal deployment:
  - allocate 20 bytes for ipv4 framing overhead and 40 bytes for ipv6.
  - allocate 20 bytes for TCP framing
  - allocate 40 bytes for TCP options overhead.

#### TLS compression

- little-known feature of TLS is built-in support for lossless compression of data transferred within the record protocol, algo is negotiated on TLS handshake and applied prior to encryption.
- However advisable to disable compression by default
  - CRIME attack leveraged it to get auth cookies and perform session hijacking
  - not content aware and may compress compressed data.
- Double compression wastes CPU time on noth client and server.
- ENsure though that it Gzip all text based assets and optimal one for all other formats.

#### Cerificate Chain length

- verify that the server does not forget to include all certs when handshake is performed.
- don;t include unnecessary cert in your chain though.
- Aim to minimise size of certificate chain.
- Sent on TLS runnigng on slow-start TCP connection adding another roundtrip to the handshake
- Minimize number of intermediate CAs. should include two, your site and CAs intermediary cert use this as CA selection criteria.
- Root CA cert should already be in broser so dont include in chain.
- should be as low as 2 or 3 KB.

#### OCSP stapling

- server can include the OCSP response from the CA to its certificate chain, allowing browser to skip online check.
- However pay attention tha t it does not overflow congestion window.
- Only one response can be included.
- See if server supports this feature in its docs.

#### HTTP Strict Transport Security

- security policy mechanism that allows server to declare access rules to a compliant browser via a HTTP header.
- max-age field.
- converts the origin to an HTTPS-only destination and helps protect the application from passive and active network attacks against user.

#### Testing and Verification 

- Familiriase yourself with the openssl commandline interface, which will help you inspect the entire handshake and configuration of your server locally.


## HTTP

- This is the common language between clients and servers.
- HTTP 0.9: The One-line Protocol.
  - High level design goals
    - file transfer functionality
    - ability to request an index search of a hypertext archive.
    - format negotiation
    - ability to refer client to another server.
  - Prototype built with following features.
    - Client request is a single ASCII character string.
    - Client request is terminated by a carriage return(CRLF)
    - Server response is an ASCII character stream.
    - Server response is HTML.
    - Connection terminated after document transfer is complete.
  - Apache and Nginx still support it given there isnt much to it.

- HTTP 1.0: Rapid growth and Informational RFC
  - Growing list of desired capabilities of the nascent web and their use cases on the public web quickly exposed many of the fundamental limitations of HTTP 0.9
  - RFC 1945, Informational not a spec or standard.
  - Key protocol changes
    - request may consist of multiple newline separated header files.
    - response object is prefixed with a response status line.
    - response has its own set of newline separated header fields
    - reponse object is not limited to hypertext, could be image, plain text or any content type.
    - connection closed after every request.
    - Other features included
      - content encoding
      - character set support
      - multipart types.
      - authorization.
      - caching
      - proxy behaviour
      - data formats.

- HTTP 1.1:Internet Standard
  - RFC 2068, RFC 2616
  - Introduced alot of critical performance optimizations:
    - keepalive connections
    - chunked encoding transfers
    - byte-range requests
    - additional caching mechanisms
    - transfer encoding
    - request pipelining

- HTTP 2.0: Improving Transport Performance
  - RFC 2616 has served as the foundation for the unprecedented growth of the internet, billions of devices...
  - A feature of HTTP is the typing and negotiation of data representation, allowing systems to be built idnependently of the data being transfered.
  - Primary focus of HTTP 2.0 is on improving transport performance and enabling both lower latency and higher throughput.
  - Non of the high level protocol semantics are affected.

### Primer on Web performance

- In any complex system a large part of performance optimization is untangling of the interactions between the many distinct and separate layers of the system each with its own set of constraints and limitations.
- End-to-end picture of web performance optimization:
  - Impact of latency and bandwidth on web performance
  - TCP constrains imposed on HTTP.
  - Features and shortcomings of the HTTP protocol itself
  - Web app;ication trends and performance requirements
  - Browser constraints and optimizations.
- No one solutions as the layers themselves keep changing, browsers getting faster, web apps profiles change  

#### Hypertext, Web pages and Web Applications

- Hypertext
  - plain text versions with some basic formatting and support for hyperlinks.
- Web pages
  - Extended hypertext definition to support additional hypermedia resources, images, audio.
- Web Apps
  - DHTML and AJAX allowed for response to user action direclty in the browser.
- Page Load Time(PLT) - time to onload event in browser, event is fired once a document and all its dependent resources have finished loading.
- With web apps PLT becomes an insufficient performance benchmark as we also want to answer application specific questions.
  - What are the milestones in the loading progrss of the app?
  - What are the times to first interaction?
  - What are the interactions user should engage in?
  - What are the engagements and conversion rates for each user?
- The success of your performance and optimization strategy is directly correlated to your ability to define and iterate on application specific benchmarks and criteria.
- Nothing beats application specific knowledge and measurements, esp when linked with bottom line goals and metrics of your business.
- HTML DOM and CSS CSSOM are combined to create a render tree used to perform a layout and paint on the screen.
- Script execution though can issue a synchronous doc.write and block DOM parsing and construction.
- Similarly scripts can also query for a styled version of an object meaning block on CSS too.
- Interaction is intertwined meaning, DOM cannot proceed until Js is executed and Js waits on CSSOM.
- Performance of your application, esp first load and time to render depends on dependency graph resolution strategy.
- Styles at the top, scripts at the bottom.

#### Anatomy of Modern Web Application

- refer to HTTP Archive website, peridically crawls popular sites for statistics on this.
- Web apps are running the installation process on each and every visit.
- Speed, performance and user perception
  - If application mist react and respond to a user, we must plan and desing for specific, user-centric perceptual processing time constants.
  - Aim for rendering in under 250ms to keep user engaged
  - Faster sites yield more page views, higher engagement and higher conversion rates.
- Analyzing resource waterfall
  - is likely the single most insightful network performance and diagnostic tool at our disposal
  - Webpagetest.org, tests performance of web pages from multiple locations around the world.
  - recognize that every HTTP request is composed of a number of separate stages: 
    - DNS resolution, TCP connection handshake, TLS negotiation, dispatch of the HTTP request, Content download.
  - HTML parsing is done incrementally, allowing browser to discover required resources early and dispatch the necessary request in parallel.
  - Scheduling of when resource is fetched is in large part determined by the structure of the markup., browser may prioritze some but incremental discovery of each resource in document is what creates the distinct resource "Waterfall effect"
  - Above is front-end performance analysis
  - Resorce waterfall lookup(individual HTTP request) vs the connection view(lifetime of each TCP connection)
  - Main bottlenect in web applcations is network latency between client and server and not the bandwidth.
- Performance pillars: Computing, Rendering, Networking
  - execution of a web program involves: Fetching resource, page layout and rendering and Js execution.
  - Rendering and scripting steps follow a single threaded interleaved model of execution, not possible to perform concurrent mods on the DOM.
  - Fast and efficieent delivery of network resources is the performance keystone of each and every application running in the browser.
  - Roundtrip latency matters most.
- Synthetic and real user performance measurements
  - No single metric holds true for all applications which means custom metrics defined for each case.
  - Perfomance data then gatjered using synthetic and real user perf measurements.
  - Synthetic testing refers to any process with a controlled measurement environment: local build running through performance suite, load testing against staging infrastructure 
    or a set of geo-distributed monitoring servers that periodically perform a set of scripted actions and log the outcomes.
  - Each and every one of these tests may test a different piece of infastructure and serves as a stable baseline to help detect regressions or narrow in on a specific component of the system.
  - However synthetic is not a one catch all solution and may fail agianst
    - Different user page navigation behaviour
    - Browser Cache
    - Intemediaries: proxies and caches may differ.
    - Diveristy of hardware: range of CPUs GPUs and memory perfomance
    - Browser diversity: 
    - Connectivity
  - To solve for above supplement synthetic startegy with Real User Measurements....Navigation Timing API, User Timing, Resource Timing
  - Navigation Timing API
    - DNS and TLS connect times via standardized performance.timing object
  - Resource Timing 
    - provides data for each resource on the page, allowing for full profile of the page.
  - User Timing  
    - mark and measure application-specific performance metrics with help of high-resolution timers.
- Browser Optimization
  - modern browser is more than just a simple network socket manager.
  - broad categories
    - Document-aware optimizations
      - networking stack is integrated with the document, css and js parsing pipelines to help identify and prioritize  critical network assets, dispatch them early and get the page to an interactive state as soon as possible.
      - this is via  resource priority assignments, lookeahead parsing.
    - Speculative optimizations
      - browser may learn user naviagtion patterns over time and perform speculative optimizations in an attempt to predict the likely user actions by pre-resolve and pre-connect.
  - techniques
    - Resource pre-fetching and prioritization
      - parsers communicate extra info to network to indicate ralative priority of each resource
    - DNS pre-resolve
      - user-action triggered, i.e hover over link, learned navigation
    - TCP pre-connect
      - on DNS pre-resolve, TCP connection may be opened in anticipationof a HTTP request
    - Page pre-rendering
  - How can we assist browser in performance optimization
    - pay close attention to the structure and delivery of each page
      - critical resources such as CSS ad Js should be discoverable as early as possible in the document.
      - CSS should be delivered as early as possible to unblock rendering and Js execution.
      - Noncritical Js should be deferred to vaoid DOM and CSSOM construction
      - HTML document is parsed incrementally by the parser: hence document should be periodically flushed for better performance.
    - additionally embed hints into document itself to tip off browser about additional optimizations it can perform on our behalf
      - check browser support for speculative browser optimizations hints.
  
## HTTP 1.X

- HTTP 1.0 best optimization is upgrading to HTTP 1.1
- Among the features introduced
  - Persistent connections to allow connections eruse
  - Chunked transfer encoding to allow response streaming
  - Request pipelining to allow parallel request processing
  - Byte serving to allow range-based resource requests
  - Improved and much better specified caching mechanism
- Based on Steve Souders High Perfomance Websites book, networking optimizations include:
  - Reduce DNS lookups
  - Make fewer HTTP requests
  - Use a CDN.
  - Add an Expires header and configure ETags
  - Gzip assets
  - Avoid HTTP redirects
- Benefits of keepalive connections
- HTTP pipelining
  - allows for relocation the FIFO queue fron the client(request queueing) to the server(response queueing) created by preceding HTTP persistence feature.
  - eliminates also request propagation latency and request propagation latency to allow server to process requests in parallel 
- Important limitation of HTTP 1.X is strict serialization of returned responses, does not allow data from multiple requests to be interleaved on same connection.
- Head-of-line blocking also enountered here for requests that preceded others have to finish first.
- The best way to deploy HTTP pipelining is to create a secure (HTTPS) tunnel between the client and server.
- Using multiple TCP connections
  - most modern upto 6 connections per host.
  - meditate on the implications of that.
    - competition for shared badnwidth
    - limited application parallelism even in parallel TCP streams
    - implementation complexity for handling collections of sockets.
    - extra memory buffers and CPU overhead.
  - still desirable because:
    - workaround to limitations of the application protocol HTTP
    - workaround for low starting congestion window size in TCP
    - workaround for clients that cannot use TCP window scaling
- Domain Sharding
  - instead of serving all resources from the same origin, we can manually shard them across multiple subdomains.
  - the higher the shard count the higher the parallelism.
  - not a quick solution as domain has to be managed, DNS lookup costs apply
- Measuring and Controlling Protocol Overhead
  - each HTTP request will carry 500-800bytes of metadata...without cookies used for session management, analytics and personalization.
  - reducing transferred header data, highly repetitive and uncompressed could save entire roundtrips pf network latency and improve app performance.
- Concatenation and Spriting
  - Concatenation 
    - multiple javascript or css files are combined into a single resource
  - Spriting
    - multiple images are combined into a larger, composite image.
  - Advantages
    - Reduced protocol overhead: one file's overhead is smaller than many files overheads
    - Application-layer pipelining: moving pipelining logic one layer above into our application
  - They are both types of content aware application layer optimizations which can also bring about additional preprocessing, deployment considerations and code.
  - May have negative impact on cache performance and execution speed of pages
  - We sacrifice modulariy and cache granularity which can quickly backfire if there is a high churn on the asset and especially if the bundle is large.
- Calculating Image memory requirements
  - stored as memory backed RGBA bitmaps within the browser
  - each RGBA image pixel requires four bytes of memory (red 1, green 1, blue 1, transparency 1)
  - hence total memory = pixel width * pixel height * 4 bytes
  - 800 * 600 * 4 = 1.83MB
- Js and CSS processing models dont allow incremental execution, whole file has to be there.
- CSS and JS bundle size vs execution performance - idela bundle sizes??
- Resource Inlining 
  - embed the resource within the document itself.
  - base64 encoding used for non-text assets adding a 33% byte expansion overhead.
  - consider inlining resource under 1-2 KB.
  - bundling vs inlining, high use across site vs small limited
  

## HTTP 2.0

- move the workarounds in our application while using HTTP 1.1 to the transport layer itself, opening a whole new way to optimize our applications and improve performance.
- Primary goals
  - reduce latency by enabling full request and response multiplexing
  - minimize protocol overhead via efficient compression of HTTP header fields.
  - support for request prioritization.
  - Support for server push.
- Does not modify application semantics in any way and core concepts remain in place.
- Also enables alot of new optimizations our application can leverage, previously not possible.

### SPDY

- Project goals
  - target a 50% reduction in page laod times
  - Avoid need for change to content by website authors
  - Minimize deployment complexity, avoid changes in network infrastructure.
  - Develop it in collaboration with Open source community.
  - Gather real data to invalidate experimental protocol.
- Sparket HTTP 2.0 track

### Road to HTTP 2.0

- Key design criteria
  - Improve end-user perceived latency over HTTP 1.1 using TCP.
  - Address head-of-line blocking 
  - Not require multiple connections to a server to enable parallelism, thus improving its use of TCP, esp on congestion control.
  - Retain semantics of HTTP 1.1
  - Define how HTTP 2.0 interacts with HTTP 1.x intermediaries.
  - Identify extensibility points and policy for their appropriate use
- Reason for major revision increment to 2.0 is due to change in how data is exchanged between client and server.
- HTTP 2.0 adds a new binary framing layer which is not backward compatible 
- SPDY serves as vehicle for experimenting with new features and proposals for HTTP 2.0

### Desing and Implementation goals

- Binary Framing Layer
  - layer refers a design choice to introduce a new mechanism between the socket interface and the higher HTTP API exposed to our application.
  - both client and server must use the new binary encoding mechanism to understand each other.

- Streams, Messages and Frames
  - Stream
    - a bidirectional flow of bytes within an established connection
  - Message
    - a complete sequence of frames that map to a logical message.
  - Frame
    - smallest unit of communication in HTTP 2.0, each containing a frame header, which at minimum identifies the stream to which the frame belongs
  - All HTTP communication is performed within a connection that can carry any number of bidirectional streams, each stream communicates in messages, which contain one or multiple frames. each which can be interleaved 
    and then reassembled via the embedded stream identifier in the header of each individual frame. 

### Request and Response Multiplexing

- The ability to break down a HTTP message into independent frames, interleave then and then reassemble them on the other end is the single most important enhancement of HTTP 2.0
- This eliminates need for HTTP 1.X workarounds such as concatenated files, image sprites and domain sharding.

### Request Prioritization

- Once a HTTP message can be split into many individual frames, exact order of interleaving nd delivery can be optimized for further improvement to perfomnce of our apps.
- Each frame is assigned a 31-bit  priority value
- Different strategies applied to deal with streams, messages and frames in an optimal way  depending on the priority values.
- Client should provide good priority data.

### One connection per Origin

- Due to the new binary framing mechanism in place, no need for multiple connections to multiplex connections in parallel , each stream can now be split into many frames.
- all HTTP 2.0 connections are persistent.
- Also reduces connection overhead, fewer connections to manage, smaller memory footprint, better connection throughput, better compression through use of single compression context, improved network congestion, less itme in slow-start and faster congestion and loss recovery.

### Flow control

- Multiplexing multiple streams over the same TCP connection introduces contention for shared bandwidth resources.
- Stream priorities can help determine relative order of delivery but priorities alone are insufficient to control ow resource allocation is performed between streams and multiple connections.
- On HTTP 2.0 connection, client and server exchange SETTINGS frame, which sets the flow control window sizes in both directions.
- Similar to TCP flow control in that it is hop-by-hop, meaning its based on window update frames.

### Server Push

- Ability of a server to send multiple replies to a single client request.
- In addition to responses, it can also push resources not explicitly requested by the client.
- One connection, SETTINGS frame can limit maximum number of concurrent streams in both directions, hence client can limit number of pushed streams or disable server push entirely.
- This is useful in the case of multiple resources where the server pushes them without explicit request, hence reducing request latency.
- Inlining is an example of server push
- Pushed resources can be cached, declined by client, reused across different pages and prioritized by server.
- All pushed resources are subject to sam eorigin policy, must be authoritative for provided content.
- PUSH_PROMISE frame sent with a response, indicate intent to push.

### Header Compression

- Compresses header metadata
- Use of header tables on both sides to prevent retransmission of same data on each request and response by storing previously sent key-value pairs.
- Header tables persisted for each connection and incrementally updates by client and server
- Each new header key-value pair is either appended to the existing table or replaces a previous value in the table.
- All header keys are lowercase and request line is split into individual :method, :scheme, :host and :path key-value pairs.
- Second request needs only communicate only the single path header that has changed between requests, all other headers are inherited from previous working set.
- Avoids transmitting redundant header data.

### Efficient HTTP 2.0 Upgrade And Discovery

- Most browsers use efficient background update mechanisms
- Cases to consider
  - Initiate a new HTTP connection via TLS and ALPN(Application Layer Protocol Negotiation)
  - Initiate a new HTTP connection with prior knowledge
  - Initiate a new HTTP connection without prior knowledge
- HTTP upgrade mechanism to negotiate appropriate protocol.
- Both client and server have to send a connection header which is a well known sequence of bytes defined in standard.
- Client can also choose to obtain HTTP 2.0 support information in another way..DNS record, manual configinstead of relying on upgrade workflow.

### Binary Framing Primer

- Offers more compact representation and is both easier and more efficient to process in code.
- All frames share a common 8 byte header, which contains length of frame, its type, a bit field of flags and a 31-bit stream identifier.
  - 16bit length prefix tells us a single frame can carry 2^16 - 1 bytes of data, which excludes the 8-byte header size.
  - 8-bit type field determines how rest of frame is interpreted.
  - 8-bit flags field allows different frame types to define frame-specific messaging flags
  - I-bit reserved flags always set to 0
  - 31-bit stream uniquely identifies the HTTP 2.0 stream
- *write a simple parser to examine any HTTP 2.0 bytestream and take it apart*
- HTTP standard defines types of frames
  - DATA, HEADERS, PRIORITY, RST_STREAM, SETTINGS, PUSH_PROMISE, PING, GOAWAY, WINDOW_UPDATE, CONTINUATION.

### Framing Layer workflow

- Initiating a new stream
  - client init start with HEADER frame
  - server init start with PUSH_PROMISE frame
  - payload sent separately within DATA frames.
  - stream counters are even-offset for clients IDs and odd-offset for server IDs, eliminating collision in stream IDs.
- Exchanging application data.
  - payload can be split between multiple DATA frames with the last frame indicating the end of message by toggling the END_STREAM flag in the header of the frame.
  - choice of encoding deferred to the application or server
  - HTTP 2.0 requires DATA frame not exceed 2^14 even though 2^16 is available to reduce head-of-line blocking, exceeding this should be broken up into multiple DATA frames.

### Analyzing HTTP 2.0 Frame Data Flow

- leaarn how to do this.


## Optimising Application Delivery  

- We can't control network weather between client and server , nor client hardware or the device configuration.
- TCP, TLS configuration on the server and dozens of application optimizations to account for the pecularities of the different physical leyers, versions of HTTP in use as well general app best practices.
- Physical properties of the communication channel set hard performance limits on every application.
  - speed of light and distance determine propagation latency
  - choice of medium(wired or wireless) determines processing, transmission, queueing and other delays incurred by each data packet.
- Apply all optimization to minimise or eliminate unnecessary round trips, requests and minimize the distance traveled by each packet
- Developing and investing into appropriate  measurement tools and application metrics is top priority.
- Evergreen Performance best practices
  - Cache resources on the client
    - cache specific headers, cache-control, last-modified, ETag
  - Compress assets during transfer
    - choose the right image compression algo/ format.
    - webp 26% smalller than png, 34% smaller than jpeg, supports lossless compression
    - hiher amount of cpu processing for decoding 1.4 times compared to jpeg
  - Eliminate unnecessary request bytes
    - HTTP is stateless, meaning server does not have to store data about client between different requests
    - HTTP state management mechanism, RFC 2965, allows for cookie metadata saving and update
    - most browser enforce a 4KBlimit as non is specified, should be monitored judiciously
    - take advantage of shared session cache on server to lookupother metadata.
    - also allows for many cookies per origin
  - Parallelize request and response processing
  - Apply protocol specific optimizations
  
  
## Browser APIs and Protocols

### XMLHTTPRequest

- also referred to as XHR.
- this is a browser level API that enables the client to script data transfers via Js.
- made its debut in IE in AJAX.
- XHR enabled fetching of state updates asynchronously and under full control of the application Js code.
- XHR enabled us to make the leap from building pages to building interactive web applications in the browser.
- XHR is an application API provided by the browser, meaning the browser takes care
  - manages connection establishment, pooling and termination
  - determines the best HTTP(S) transport
  - handles HTTP caching, redirects and content-type negotiation
  - enforces security, authentication and privacy constraints
  - formatting HTTP requests.
- This enabled also every networking use case to take advantage of it, scripted donwloads, uploads, streaming and real-time notifications.
- Applications can focus on initiating requests, managing their progress and processing returned data from the server.

### CORS

- XHR interface enforces strict HTTP semantics on each request, application supplies the data and URL and the brpwser formats the request and handles the full lifecycle of each connection.
- Even though application can add custom header with setRequestHeader(), there are a number that are off limits, protected headers.
- Protecting the origin headeris esp important as it is the key piece of the same-origin policy applied to all XHR requests.
- Origin is the triple of application protocol, domain name and port number.
- Cross Origin Resource Sharing provides a secure opt-in mechanism for client-side cross-origin requests.
- The opt-in authentication mechanism for the CORS request is handled at a lower layer: when request is made, browser automatically appends the protected Origin HTTP header,
  which advertises the origin from where the request is made.
- Remote server is then able to examine the Origin header and decide whether to allow request by returning an Access-Control-Allow-Origin header
- If not CORS aware, client request will fail.
- CORS takes a number of additional security precautions
  - CORS requests omit user credentials such as cookies and HTTP authentication.
  - Client is limited to issuing simple cross-origin requests which restricts both the methods allowed and access to HTTP headers that can be sent and read by the XHR.
- To enable cookies and HTTP authentication, client must set and extra property(withCredentials) on the XHR object and server must respond with an appropriate header to indicate
  that it is knowingly allowing application to include private user data.
- Client must send a pre-flight request if it needs to write or read custom HTTP headers or wants to use a non-simple method.

### Downloading data with XHR

- XHR can transfer both text-based and binary data.
- Browser offers automatic encoding and decoding for a variety of native data types, allowing application to pass these types directly to XHR to be properly encoded.
- Types:
  - ArrayBuffer
  - Blob
  - Document
  - JSON
  - Text

### Uploading data with XHR

- Pass in a data object when calling send() on the XHR request.
- send() method accepts one of DOMstring, Document, FormData, Blob, File or ArrayBuffer objects, automatically performs the appropriate encoding, sets the appropriate HTTP content-type,
  and dispatches the requests.
- Monitoring upload and download progress 
  - XHR provides APIs to monitor progress events
    - loadstart
    - progress
    - error
    - abort
    - load
    - loadend

### Streaming data with XHR

- In some cases an application may need or want to process a stream of data incrementally, upload data to the server as it becomes available or process downloaded data.
- Send() expects full payload
- Lack of streaming as a first class use case is a well recognized limitation
- *look into Streams API*

### Real-time notifications and delivery

- XHR enables a simple and efficient way to synchronize client updates with the server, request is dispatched by client to server to update appropriate data
- What about the reverse??
- Client has to poll the server for updates
- Polling is simple to implement but very ineffective, choice of polling intervals is critical, long = delays, short = traffic congestion
- Average message latency delay??
- Overhead of polling??
- Long Polling with XHR 
  - instead of returning an empty response, keep connection idle until update is available.
  - reduces number of requests and offers best case scenario for message latency.
  
## Fetch API

--- this will go here ---

## Server-Side Events

- SSE enables efficient server-to-client streaming of text-based event data: real-time notifications or updates geenrated by the server.
- SSE introduces new two components
  - a new EventSource interface in the browser which allows the client to receive push notifications from the server as DOM events
  - event stream data format, which is used to deliver the individual phases.
- Combination of these two is what makes SSE both an efficient and indispensable tool for handling real-time data in the browser.
  - Low latency delivery via single, long-lived connection
  - Efficient message parsing with no unbounded buffers.
  - Automatic tracking of last seen message and auto reconnect
  - Client message notifications as DOM events
- Under the hood, SSE provides an efficient, cross-browser impelementation of XHR streaming: actual delivery of the messages is done over a single long-lived HTTP connections.

### EventSource API

- The eventsource interface abstracts all the low-level connection establishment and message parsing behind a simple browser API.
- TO start; we need to specify the URL of the SSE event stream resource and register apporpriate Js event listeners on the object
- SSE provides a memory efficient implementation of XHR streaming, unlike a raw XHR which buffers full rceived response, SSE can discard them without accumulating all of them in memory.
- This API also provides auto-reconnect and tracking of last seen message.

### Event Stream protocol

- An SSE event stream is delivered as a streaming HTTP response, client initiates a regular HTTP request and server responds with a custom "text/stream" content-type and then streams the UTF-8 encoded event data 
- Event-stream protocol is trivial to understand and implement
  - Event payload is the value of one or more adjacent data fields.
  - Event may carry an optional ID and event type string
  - Event boundaries are marked by newlines.
- All event source data is UTF-8 encoded.
- SSE was specifically designed as a simple,efficient,server-to-client transport for text based data.
- For binary data, Websockets is the right tool for the job.

## WebSocket

- Enables bidrectional, message-oriented streaming of text and binary data between a client and server.
- It is the closest API to a raw network socket in the browser, except browser provides a simple API abstracting all the complexity
- Also provides:
  - Connection negotiation and same origin policy enforcement.
  - Interoperability with existing HTTP infrastructure.
  - Message-oriented communication and efficient message framing
  - Subprotocol negotiation and extensibility.
- This is the one of the most versatile and flexible transports available in the browser.

### Websocket API

- To initiate a new connection, we need the URL of a websocket resource and a few application callbacks.
- Sample steps
  - Open a new secure WSS connection
  - Optional callbacks, error, termination, success
  - Client initiated message to the server.
  - A callback function invoked for every message from the server.
  - invoke binary or text processing logic for the received message.

### WSS and WS url schemes

- uses custom scheme, ws for plain-text communication and wss for secure.

### Receiving text and Binary data

- Websocket communication consists of messages and application code does not need to worry about buffering, parsing and reconstructing received data.
- onmessage called when all the data is available.
- internally protocol only tracks tow pieces of information about the message, length of the payload as a variable-length field and the type of payload to distinguish UTF-8 from binary transfers.
- onmessage. DOMString for text-based data and Blob object for binary data.
- a blob object represents a file-object of immutable raw data, if you don't need to modify it, keep it as is, otherwise use ArrayBuffer.

### Sending Text and Binary Data

- ws offers a bidirectional communication channel which offers message delivery in both directions over the same connection.
- can socket.send(text, json, arraybuffer, arraybufferview, blob as binary)
- send() is aynchronous
- can query bufferedAmount attribute on the socket.

### Subcontrol Negotiation

- No equivalent mechanism like HTTP headers for communicating additional information as such client and server must agree to implement their own subprotocol to communicate this data.
- For this ws provides a simple and convenient subprotocol negotiation API.
- if subprotocol negotiation is successful, then the onopen callback is fired on the object and application can query the protocol attribute on the websocket object to determine the chosen protocol.

### Websocket API

- consists of two high-level components: opening HTTP handshake used to negotiate parameters of connection and a binary message framing mechanism to allow for low overhead, message-oriented delivery
- Websocket protocol is a fully functional, standalone protocol that can be used outside the browser although its main application is in browser-based applications 
- Binary Framing Layer
  - ws uses a custom binary framing format, which splits each application message into one or more frames, transports, reassembles them and notifies receiver.
  - websoket frame: 2-14 bytes+payload.
  - payload of all client initiated frames is masked using the value specified in the frame header, prevents cache poisoning attacks on the clients' intermediaries that dont understand ws.

### Protocol Extensions

- Ws specification allows for protocol extensions: the wire format and the semantics of the ws protocol can be extended with new opcodes and data fields.
- allows client adn server to implement additionally functionality without on top of the base Websocket framing layer without requiring any intervention or cooperation from application code.
- Extension examples
  - A multiplexing extension for websockets
    - provides a way for separate logical ws connections to share an underlying transport connection
    - addition of channel ID
  - Compression extensions for websocket
    - framework for creating ws extensions that add compression functionality to the ws protocol

### HTTP Upgrade Negotiation

- To enable one or more extensions, client must advertise them in the initial upgrade handshake and the server must ack the extensions that will be used for the lifetime of the negotiated connections
- leverages HTTP to perform the handshake offers several advantages i.e makes them compatible wiht existing HTTP infra'
- ws servers can run on port 80 and 443.
- extend and reuse the HTTP upgrade with custom ws headers.
  - Sec-websocket-version
  - sec-websocket-key
  - sec-websocket-accept
  - sec-websocket-protocol
  - sec-websocket-extensions
- ws connections subject to the same-origin policy.

### WS use cases and performance

- Request and Response streaming
- Message overhead
- Data efficiency and compression
- Custom application protocols
- Deploying Websockets Infrastructure


## WebRTC

- collection of standards, protocols and Js APIs, the combination of which enables peer-to-peer audio, video and data sharing between browsers(peers).
- turns real-time communication into a standard feature that any web application can leverage via a simple Js API.
- Primary APIs
  - MediaStream: acquisition of audio and video streams
  - RTCPeerConnection: communication of audio and video data
  - RTCDataChannel: communication of arbitrary application data.
- WebRTC transports its data over UDP.
- primary purpose is enable real-time communication between browsers and its also designed such that it can be integrated with existing communication systems: VOIP, SIP clients and PSTN
- WebRTC is also about bringing all the capabilities of the web to the telecommunications world.

### Audio and Video Engines

- requires the browser access the system hardware to capture both audio and video via a simple and conssitent API.
- raw audio and video streams are not sufficient on their own, each stream processed to enhance quality,synchronized and output bitrate adjust to bandwidth and latency between clients.
- capturing and processing audio and video is a complex problem
  - Internal WebRTC API - Voice engine and Audion Engine
  - Audio Engine - Audio codecs, jitter/packet loss concealement, echo cancellation, noise reduction
  - Video Engine - Video codecs, jitter/packet loss concealement, synchronization, image enhancement
- Acquiring audio and video streams with getUserMedia
  - MediaStream object enables application to request audio and video streams as well as a set of APIs to manipulate and process the acquired media streams.
  - The mediastream object consists of one or more individual tracks.
  - Tracks within it are synchronized.
  - Input source can be a physical device; microphone, webcam, local or remote file.
  - Output stream can be sent to one or more destinations; local video or audio element, js code for post-processing or a remote peer.
  - all audio and video processing is handled automatically by audio and video engines.
  - getusermedia() is responsible for requesting access to the microphone and camera from the user and acquiring those that match the specified constraints
  - Audio(OPUS) and Video(VP8) Bitrates.

### Realtime Network Transports

- Audio and Video streaming apps are designed to tolerate intermittent packet loss, audio and video codecs can fill in small data gaps, often with minimal impact on the output quality
- Applications must implement their own logic to recover from lost or delayed packets carrying other types of application data.
- Timeliness and low latency can be more important than reliability 
- Thus preferrance for UDP over TCP for realtime data delivery.
- WebRTC uses UDP at the transport layer.
- WebRTC also requires a large suporting cast of protocols 
  - ICE: TURN and STUN
  - SDP - session description protocol
  - DTLS - datagram transport layer security
  - SCTP - stream control transport protocol
  - SRTP - secure real-time transport protocol
- ICE, TUNR, STUN are necessary to establish and maintain a peer-to-peer connection over UDP
- DLTS is used to secure all data transfers between peers, encryption is a mandatory feature of WebRTC.
- SCTP and SRTP are the application protocols used to multiplex the different streams, provide congestion and flow control and provide partially reliable delivery and other additional services over UDP.

### RTCPeerConnection API

- responsible for managing the full lifecyle of each peer-to-peer connection.
- manages
  - full ICE workflow for NAT traversal.
  - sends automatic(STUN) keepalive between peers.
  - keeps track of local streams.
  - keeps track of remote streams.
  - triggers automatic stream renegotiation as required.
  - provides APIs to generate the connection offer, accept the answer, allows us to query the connection for its current state
- Establishing a peer-to-peer connection
- Signaling and session negotiation
  - WebRTC defers the choice of signaling transport and protocol to the application, allowing for interoperability with a variety of signaling protocols powering existing communications infrastructure.
  - Session Initiation Protocol(SIP)
    - VoIP
  - Jingle
    - signaling extension for the XMPP protocol used for session control
  - ISDN User Part(ISUP)
    - used for setup of phonecalls in many PSTN
  - Custom signaling channel
    - skype uses one of this.
  - Look into Asterisk popular for communication needs across businesses and large carriers
- Session Description Protocol
  - used to describe the parameters of the peer-to-peer connection.
  - does not deliver any media itself used to describe the session profile(types of media to be exchanged,network transports, used codecs, bandwidth information, other metadata)
  - JavascriptSessionEstablishmentProtocol(JSEP) abstracts all the inner workings of SDP behind a few simple method calls on the RTCPeerConnection object.
- Interactive Connectivity Establishment
  - each RTCPeerConnection object contains an "ICE agent"
  - the primary goal for the ICE agent is to identify a viable routing path between the peers, however it doesnt stop there, periodically checks other candidates to see if it can deliver better performance via an alternate route
    - ICE agent is responsible for gathering local IP, port tuples(candidates)
    - ICE agent is responsible for performing connectivity checks between peers.
    - ICE agent is responsible for sending connection keepalive.
  - Incremental provisioning(Trickle ICE)
    - extension to the ICE protocol that allows incremental  gathering and connectivity checks between the peers.
    - relies on the signaling channel as they are discovered.
  - Tracking connectivity status and ICE gathering
    - a successful connection is one that is able to establish connectivity for all requested streams

### Delivering Media and Application Data

- Other protocols fill in the gaps left by the UDP connection
  - DTLS 
    - used to negotiate the secret keys for encrypted media data and for secure transport of application data.
    - WebRTC would use TLS for this but it cant be used over UDP as it relies on reliable and in-order delivery offered by TCP
    - DTLS is TLS for UDP.
    - extends the base TLS record protocol by adding an explicit fragment offset and sequence number for each handshake record allowing large records to be fragmented across packet and reassembled by the other peer.  
  - SRTP 
    - used to transport audio and video streams
    - defines a standard packet format for delivering audio and video over IP networks.
    - provides all the essential information required by the media engine for real-time playback of the stream.
  - SCTP 
    - used to transport application data.     
    - responsible for how the individual SRTP packets are delivered, by implementing a separate, out-of-band feedback channel for each media stream.
    - tracks all statistics related to delivery of packets.
    - provides the best features of TCP and UDP.
    - Concepts
      - Association
      - Stream
      - Message
      - Chunk

### Data Channel

- enables bidirectional exchange of arbitrary application data between peers, think Websocket, but peer-to-peer and with customizable delivery properties of the underlying transport.
- mirrors websockets with some minor differences
  - datachannel is a factory method on the RTCPeerConnection object
  - either peer can initiate a new datachannel session.
  - each datachannel can be configured with custom delivery and reliability semantics.
- setup and negotiation
- configuring message order and reliability
  - allows us to customize the delivery semantics of each channel to match the requirements of the application and the type of data being transferred.
    - in-order or out-order delivery of messages
    - reliable or partial reliable delivery of message
      - partial reliable delivery with retransmit
      - partial reliable delivery with timeout

## HTTP AGAIN!!!

- Web servers and clients
- Resources
- Transactions
- Messages
- Connections
- Architectural components of the Web

### URLs and Resources

- Uniform Resource Locators are the standardized name for the internet's resources.
- URLs are a subset of a more general class of resource identifier called a uniform resource identifier(URI)
- URI are a general concept consisting of a two main subsets URLs and URNs
- URNs identify resources by name regardless of where they reside.
- URL parts:
  - first part is the scheme: tells web client how to access the resource: HTTP
  - second part is the server location: tells it where its located.
  - third part is the resource path: what particular resource is being requested
- URLs can direct you to resources available through other protocols other than HTTP, pointing you to any resource on the internet.
- i.e email, mailto: , ftp: file transfer, rtsp: movie off streaming sites.
- URL syntax
  - <scheme>://<user>:<password>@<host>:<port>/<path>;<params>?<query>#<frag>
  - params - provide applications with additional information that they need to access the resource, name:value pairs.
  - query - introduction of a query component after ?, query component, named:value pairs separated by &. i.e database services.
  - frag - allows referencing parts or fragments of a resource, dangles off right side of URL, preceded by a # character. enforced at the client not server as HTTP doesnt have that functionality.
- Relative URLs
- Expandomatic URLs
  - hostname expansion 
  - history expansion
- URL character set
  - encoding mechanisms
    - escape character is %HX, % followed by 2 hex digits that represent that character
    - character restricctions
      - [%, /, #, ., .., :, ;, ?, &@=]
      - > 0x7F, 0x00-0x1F
- Schemes
  - http, https
  - mailto
  - ftp
  - rtsp, rtspu
  - file
  - news
  - telnet
  
### HTTP Messages

- HTTP messages are the blocks of data that are sent between HTTP applications
- Messages commute inbound to the origin server.
- Messages flow downstream

#### Parts of a message

- 3 parts
  - start line
  - block of headers containing attributes
  - optional body containing data.
- Start line and header are just ASCII text broken up by lines, each line ending with a two character end-of-line sequence.
- Entity body or message body is simply an optional chunk of data, and can contain text or binary data or can be empty.
- Message syntax
  - request messages and response messages.
  - request
    - method, request-URL, version
    - headers
    - entity-body
  - response
    - version, status, reason-phrase
    - headers
    - entity-body
- Start Lines
  - Request line
    - contains method describing what operation the  server should perform and a request URL describing which to perform the method.
    - also contains HTTP version client is speaking
  - Response line
    - contains HTTP version that response message is using, a numeric status code, textual-phrase
  - Mthods
    - GET: safe
    - HEAD: 
      - safe, only retrun the headers, 
      - find out about a resource without getting it, exists, modified.
    - POST
      - send input data to the server
    - PUT
      - writes documents to a server.
      - create a new resource or replace it.
    - TRACE
      - track path of the request
      - initiates a loopback diagnostic at the server.
      - diagnostics
    - OPTIONS
      - asks server to tell us about the various capabilities of the web server.
    - DELETE
    - Not all servers implement all seven methods.
  - Status codes
    - tell client what happened
    - 100 - 199: informational.
    - 200 - 299: success
    - 300 - 399: redirection / resource have been moved
    - 400 - 499: client error
    - 500 - 599: server error
  - Reason phrases
    - paired one-to-one with status codes.
  - version numbers
    - format HTTP/x.y
    - HTTP applications to tell each other which version of the protocol they conform to.
- Headers
  - add additional information to request and response messages.
  - list of name/value pairs
  - Header classifications
    - General headers
      - can appear in both request and response messages
      - include:
        - connection
        - date
        - MIME-version
        - Trailer
        - Transfer-Encoding
        - Upgrade
        - Via
        - Caching headers
          - cache-control
          - pragma
    - Request headers
      - more information on request
      - Include;
        - client-ip
        - From
        - Host
        - Referer
        - UA-color
        - UA-CPU
        - UA-Disp
        - UA-OS
        - UA-Pixels
        - User-Agent
      - Accept Headers
        - Accept
        - Accept-charset
        - Aceept-encoding
        - Accept-language
        - TE
      - Coonection request headers
        - Expect
        - If-Match
        - If-Modified-Since
        - If-None-Match
        - If-Range
        - If-Unmodified-Since
        - Range
      - Request security headers
        - Authorization
        - Cookie
      - Proxy request headers
        - Max-forwards
        - Proxy-Authorization
        - Proxy-connection
    - Response headers
      - more information about the response.
      - Include
        - Age
        - Public
        - Retry-After
        - Server
        - Title
        - Warning
      - Negotiation headers
        - accept-ranges
        - vary
      - Response security headers
        - Proxy-Authenticate
        - Set-Cookie
        - WWW-Authenticate
    - Entity headers
      - describe body size and contents, or resource itself
      - Include:
        - Allow
        - Location
      - Content Headers
        - Content-Base
        - Content-Encoding
        - Content-Language
        - Content-Length
        - Content-Location
        - Content-MD5
        - Content-Range
        - Content-Type
      - Entity caching headers
        - ETag
        - Expires
        - Last-Modified
    - Extension headers
      - new headers not in the spec
      - allow developers to extend capabilities of HTTP services to properly manage their resources.
  - Header syntax
    - name: value
    - can be made more readable by breaking them into multiple lines, preceding each extra line with space or tab.
- Entity body
  - payload of the HTTP messages
  - can carry many types of digital data:  

## Connection Management

- TCP connections
  - TCP gives HTTP a reliable bit pipe.
- TCP streams are segmented and shipped by IP packets.
- Each IP packet contains
  - An IP packet header(20 bytes)
    - source and destination IP addresses
    - size
    - other flags
  - A TCP segment header(20 bytes)
    - TCP port numbers
    - TCP control flags
    - numeric values used for data ordering and integrity checking.
  - A chunk of TCP data
- Keeping TCP connections straight.
  - TCP keeps the many connections open straight using port numbers.
  - IP addresses get you to the right computer, port numbers get you to the right application.
  - TCP connection
    - <source-IP-address, source-port, destination-IP-address, destination-port>
    - these 4 values uniquely define a connection.
    - No two connections can have the same value for al four but can for some of the components
- Programming with TCP sockets
  - interfaces provided by the socket API
  - socket API hides all the details of TCP and IP from the HTTP programmer.
  - Include
    - s = socket(<parameters>)
    - bind(s, <local IP:port>)
    - connect(s, <remote IP:port>)
    - listen(s, ...)
    - s2 = accept(s)
    - n = read/write(s, buffer, n)
    - close(s)
    - shutdown(s, <side>)
  - socket API lets you create TCP endpoint data structures, connect these endpoints to remote server TCP endpoints, and read and write data streams.
  - TCP API hides all the details of the underlying network protocol handshaking, segmentation and reassembly of the TCP data stream to and from IP packets.
- HTTP performance relies heavily on the performance of TCP plumbing.
  - TCP performance considerations
    - HTTP transaction delays
  - Performance focus areas
    - TCP connection setup handshake.
    - TCP slow-start congestion control
    - Nagles algorithm for data aggregation.
      - RFC 896.
      - check TCP_NODELAY
    - TCP's delayed acknowledgement algorithm for piggybacked acknowledgements
      - TCP impelements this by holding outgoing acks in a buffer for a certain window of time(100-200) looking for an outgoing message to piggyback on, if none, send as own packet.
      - check if its adjustable via OS as can introduce significant delays
    - TIME_WAIT delays and port exhaustion.
      - control block of ip address and port number introduced and held for 2wice maximum segment lifetime.
      - source ports: 60,000
      - operating systems can slow down significantly when there are multiple open connections and control blocks.
- HTTP connection handling
  - Connection Header
    - can carry 3 different types of tokens
      - HTTP header field names, listing headers relevant only to that connection
      - Arbitrary token values, describing non-standard options for this connection
      - value close, indicating persistent connection will be closed when done.
    - after HTTP app receives connection with HTTP header, it parses it, applies all options then deletes header, before forwarding to the next hop.
    - placing hop-by-hop header name in connection header is called protecting the header.
  - Serial transaction delays.
    - solvable using
      - parallel connections
      - persistent connections
        - dumb proxy keep alive.
      - pipelined connections
      - multiplexed connections
  - Mysteries of Connection Close
    - at will disconnection
    - content length and truncation
    - connection close tolerance, retries and idempotency.
    - graceful connection close.
      - full and half closes
      - TCP close and reset errors
      - graceful close.

## Web Servers

- Web server processes HTTP requests and serves responses, an either be software or hardware.
- Web server implementations
  - They implement HTTP and related TCP connection handling.
  - They also manage the resources served by the web server and provide administrative features to configure, control and enhance the web server.
  - Web server logic implements the HTTP protocol, manages web resources and provides web server administrative responsibilities.
  - Web server logic shares responsibility for managing TCP connections with the operating system.
  - OS manages h/w details of the underlying computer system and provides TCP/IP network support, filesystems to hold web resources and process management to control current computing activities.
- Types:
  - General purpose s/w web servers
    - run on standard, network-enabled computer systems.
    - Apache, Ms web servers.
  - Web server appliances
    - prepackages software/hardware solutions.
    - IBM whistle, Toshiba Magnia.
  - Embedded Web servers
    - tiny web servers intended to be embedded into consumer products
- What Real Web servers do:
  - Set up connection - accept a client connection or close it if unwanted.
    - Handle New connections
    - Client hostname identification
      - reverse DNS.
    - Determining client user using ident.
      - ident allows servers to find out what username initiated the connection.
      - port 113.
  - Receive request - read a HTTP message from the network.
    - parses out the pieces of the request message.
    - internal representations of messages via custom data structures that make it easy to manipulate...i.e pointer and length of message
    - connection input/output processing architectures.
      - single threaded web servers.
      - multiprocess and multithreaded web servers.
      - multiplexed I/O servers.
      - multiplexed multithreaded web servers.
  - Process request - interpret request message and take action
  - Access resource - access resource specified in the message.
    - docroots
    - virtually hosted docroots
    - user home directory docroots.
    - directory listings
    - dynamic content resource mapping, application servers., cgi(server-side apps)
    - server-side includes(SSI)
    - access controls.
  - Construct response - create the HTTP response message with the right headers.
    - response entities
    - MIME typing
      - mime.types
      - magic typing
      - explicit typing
      - type negotiation
    - redirection
      - permanently moved resources
      - temporarily moved resources
      - URL augmentation
      - Load balancing
      - Server affinity
      - Canonicalizing directory names
  - Send response - send response back to the client.
  - Log trasnaction - places notes about the completed transaction in a log file. 

## Proxies

- Sit between clients and servers and act as middlemen shuffling packets back and forth between them.
- With a web proxy, client talks instead to it, which itself talks to the server on the client's behalf.
- HTTP proxy servers are both web servers and web clients, as they do both send and receive requests just like a web server.
- Private and Shared proxies
  - a proxy server can either be dedicated or shared among many clients, private and public.
  - common caching in public proxy.
  - private proxy on client computer to extend browser functionality.
- Proxies vs Gateways
  - proxies connect two or more apps that speak same protocol while gateways do so for different protocols.
  - gateway acts as a protocol converter, allowing a client to complete a transaction with a server on a different protocol.
  - commercial proxy servers almost always implement gateway functionality.
- Why use them?
  - provide many useful value-add web services
    - child filter.
    - document access controller
    - security firewall
    - web cache
    - surrogate or reverse proxy or server accelerators
    - content routers(netflix)
    - transcoding
    - anonymizer
- Where do they sit?
  - Proxy server deployment
    - Egress proxy
      - stick them at the exit points of local networks and larger internet.
    - Access/Ingress proxy
      - at ISP access points.
    - Surrogates
      - in front of web servers, where they can field all requests to it and only ask for resources when necessary.
      - assume name and IP address of the web server, so all requests go to it instead of server.
    - Network exchange proxy
      - internet peering exchange points between networks
  - Proxy Hierarchies
    - message passed form proxy to proxy until they eventually reach the origin server and then passed back via the proxies to the client.
    - proxies in a hierarchy are assigned parent and child relationships.
    - next inbound proxy is parent and next outbound is child.
    - Proxy hierarchy content routing
      - hierarchies are not static
      - can be dynamic in relation to
        - load balancing
        - geographic proximity routing
        - protocol/type routing
        - subscription based routing
- How proxies get traffic?
  - Modify the client
    - configure on the client side that it needs to use a proxy server.
  - Modify the network
    - net infra intercepts and steers web traffic into a proxy without client knowledge or participation
  - Modify the DNS namespace
    - manual editing or using special dynamic DNS servers that compute the appropriate proxy or server on demand.
  - Modify the web server
    - HTTP redirection command.
- Client proxy settings
  - Manual configuration
  - Browser preconfiguration
  - Proxy auto-configuration
    - small js programs that compute proxy settings on the fly
    - mime type = application/x-ns-proxy-autoconfig
  - WPAD proxy discovery
    - Web Proxy Autodiscovery Protocol
    - uses a series of resource-discovery techniques to determine proper PAC file.
      - DHCP
      - Service location protocol
      - DNS well known hostnames
      - DNS SRV records
      - DNS URIs in txt records
- Tricky things about proxy requests
  - proxy URI differ from server URIs
    - partial URI for server, full URI for proxy.
  - intercepting proxies get partial URIs
  - in-flight URI modifications
  - URI client-auto expansion and hostname resolution
    - URI reslution without a proxy
    - URI resolution with an explicit proxy
    - URI resolution with an intercepting proxy

### Tracing Messages

- You need to be able to trace the flow of messages across proxies and to detect any problems, just as it is important to trace the flow of IP packets across different switches and routers.
- The Via Header
  - lists information about each intermediate node through which a message passes.
  - each time a message goes through another node, the intermediate node must be added to the end of the Via list.
  - used to track forwarding of messages, diagnose message loops and identify the capabilities of all senders along the request/response chain.
  - proxies can also use this to detect routing loops in the network.
  - both request and response messages have Via headers.
  - Via header also record protocol conversions as some proxies support gateway functionality.
- The TRACE method
  - useful method for debugging proxy flows
  - max forwards.
- Proxy Authentication
  - HTTP provides for proxy authentication, blocks request for content until user provides valid access-permission credentials to the proxy.
- Proxy interoperability
  - Handling unsupported headers and methods
    - OPTIONS methods allows discover the supported functionality.
    - Allow header. 

## Caching

- Web caches are HTTP devices that automatically keep copies of popular documents.
- Caches benefits:
  - Reduce redundant data transfers
  - Reduce network bottlenecks
  - Reduce demand on origin servers.
  - Reduce distance delays
  
- Redundant data transfers
- eat up expensive data bandwidth, slow down transfers and overload web servers.
- Bandwidth bottlenecks
  - clients access services at the speed of the lowest network on the way.
  - bandwidth causes noticeable delays for larger documents and speed difference between network types is dramatic.
- Flash crowds
  - when an event causes many people to access a web document at nearly the same time.
- Hits and Misses
- Revalidations
  - caches have to check now and then that their copies are still up-to-date with the server, HTTP revalidations
  - to make them efficient, HTTP defines special requests that can quickly check if contents are fresh without getting the whole object from the server.
  - a cache can revalidate a copy as many times it wants but due to network constraints and size of it, only do it for requested objects or when its old enough to warrant a check.
  - revalidate hit or slow hit, when cache sends a small revalidation request to origin server and gets a 304 Not modified response.. cache marks it as temporary fresh again.
  - slow hit is slower than pure cache hit due to check but faster than cache miss.
  - If-Modified-Since header, when added to a GET request, tells server to only add it if object has been modified since last time copy was cached.
  - Types
    - Revalidate Hit
    - Revalidate Miss
    - Object deleted.
- Hit Rate
  - fraction of requests that are served from cacheis called cache hit rate.
  - often described as a precentage.
  - depends on
    - how big your cache is
    - how similar interests of the cache users are
    - how frequently data is changing
    - how cache itself is configured.
  - Hit rate is notoriously difficult to predict.
- Byte Hit Rate
  - Represents the fraction of all bytes transferred that were served from the cache.
  - captures the degree of traffic savings
- Distinguishing Hits and Misses
  - HTTP provides no way for a client to tell if a response was a cache hit or an origin server access.
  - one way is use the data header
- Cache topologies
  - public vs private caches
    - most web browsers have private caches on memory of your pc and allow you to configure cache sizes and settings.
    - proxy caches are public and have cahnce to do away with redundant traffic as its serving many clients.
    - proxy cache follow rules of proxy..manual or automatic.
  - Proxy cache hierarchies
    - idea is to use smaller, inexpensive caches near the clients and progrssively larger more powerful one up the hierarchy to hold documents shared by many users.
- Cache meshes, content routing and peering
  - some proxy caches can be described as content routers as they make routing decisions about how to access, manage and deliver content.
  - cache proxies on cache meshes make dyncamic cache communication decisions.
    - select between parent cache and origin server dynamically.
    - select particular parent cache dynamically
    - search caches in the local area before going to parent cache.
    - allow or block internet transit via cache.
  - sibling caches
- Cache processing steps
  - modern commercial proxies are quite complex as they are built to be high-performant and support advances HTTP features.
  - Common steps:
    - Receiving - cache reads arriving request message from the network.
      - High-performance caches read from multiple connections and start processing transaction before entire message has arrived.
    - Parsing - cache parses message, extract headers and URL.
      - parses and places header parts into easy-to-manipulate data structures.
      - also responsible for normalizing headers
    - Lookup - cache checks if local copy is available, if not, fetches it and stores it locally
      - use fast algorithms to determine whether an object is available in local cache.
      - cached object contains server response body and original server response headers so they can be returned by cache hit.
      - cached object also contains metadata.
    - Freshness check - checks if cached copy is fresh, if not, request a new one.
    - Response creation - creates response message with new headers and cached body.
      - Cache uses the cached server response headers as the starting point for the response and then modifies and augment the base headers.
      - Cache is responsible for adapting headers to match the client
    - Sending
    - Logging
      - most cache keep log files and statistics about cache usage.
- Keeping copies fresh
  - cached data needs to maintain some consistency with the server data.
  - HTTP uses document expiration and server revalidation
  - Document expiration 
    - lets an origin server attach an expiration data using special HTTP Cache-control and Expires headers.
    - cacn server this content until client explicitly requests object from server and not cache.
    - cache-control uses relative time(seconds) instead of absolute time
  - Server revalidation
    - check with server when object expires.
    - revalidation with conditional methods
      - if-modified-since - used in conjuction with last-modified
        - has some situations where it falls short
      - if-none-match - entity tag revalidation
        - publisher changes this on document edit
    - weak and strong validators
      - last-modified and entity tag are cache validators
      - strong change anytime the document changes
      - weak changes when significant meaning of the document changes.
- Controlling cachability
  - No-cache and no-store response headers
    - response marker no-sore forbids caching
    - response no-cache can be stored but not served to the client without revalidation.
  - Max-Age response headers
  - expires response headers
    - deprecated
  - Must-revalidate response headers
    - cannot server a stale copy without first revalidating with origin server
  - Heuristic expiration
    - LM-factor algorithm
  - Client freshness constraints
    - clients use cache control headers to tighten or loosen expiration constraints.
- Setting Cache controls
  - control HTMl caching via http-equiv
    - defines http headers that should be associated with the document
- Cache freshness algorithms
  - age and freshness lifetime.
    - age < freshness lifetime
    - age is total time since response was issued from the server(or revalidated)
    - freshness is how old it can get before expiry
- Caches and advertising
  - advertiser's dilemna vs publisher's response.
- Hit metering and usage limiting

## Integration points: Gateways, Tunnels and Relays

- HTTP is used as protocol for all web resources and also is a protocol that other applications and application protocols make use to get their job done.
- Gateways
  - this is the glue between resources and applications, app asks via HTTP or some other defined interface a gateway to handle a request and it can provide a response.
  - gateway can speak the query language to the database or generate dynamic content, acting like a portal.
  - some automatically translate HTTP into other protocols
  - Client side and Server side gateways
    - <client-protocol>/<server-protocol>
    - */HTTP or HTTP/*
  - Protocol gateways
    - you can direct HTTP traffic to gateways just like you do to proxies.
    - http/https server side security gateways
    - https/http client side accelerator gateways
  - Resource gateways
    - application server combines the destination server and gateway into a single server
    - app servers are server-side gateways that speak HTTP with the client and connect to an application program on the server side.
    - CGI - first popular API for application gateways, common gateway interface.
    - CGI is a standardized set of interfaces that web servers use in response to HTTP requests for special URLs, collect the program output, send it back in HTTP responses
    - Fast CGI developed to get over original CGI issues i.e spawning a new process..keeps a persistent daemon.
    - Server extension APIs
      - allow programmers to graft their own code and completely replace pieces of the server
  - Application interfaces and Web services
    - web services 
      - set of standards and protocols that allow web applications to talk to each other.
      - exchange info over XML and SOAP(simple object access protocol) aka adding xml to http.
- Tunnels
  - let you send non-HTTP traffic via HTTP connections, allowing other protocols to piggyback off HTTP.
  - establish HTTP tunnels using CONNECT
    - HTTP CONNECT establishes tunnels
  - data tunneling, timing and connection management
  - SSL tunneling
    - tunnels first developed to carry encrypted SSL traffic through firewalls
    - many orgs funnels data via packet-filtering and proxy-servers to enhance security.
    - SSL cannot be proxied by traditional proxy servers as information is encrypted.
  - SSL tunneling vs HTTP/HTTPS Gateways
    - https can be gatewayed as other protocols, having the gateway initiate the SSL session with the remote HTTPS server and then perform HTTPS transaction onthe clients part.
    - response will be received and decrypted by the proxy and sent to the client over insecure http
    - no need for SSL on the proxy.
  - Tunnels authentication
    - security considerations
      - tunnels gateway cannot verify that the protocol being spoken is really what is supposed to be tunneled
- Relays
  - simple HTTP proxies that do not fully adhere to the HTTP specification
  - process enough HTTP to establish connections, then blindly forward bytes.
  - used for simple filtering, diagnostics or content transformations
  
## Web Robots

- S/w programs that automate a series of web transactions without human interactions.
- a.k.a web crawlers, bots, worms.
- Web crawlers
  - robots that recursively traverse information webs.
  - have to give it a starting point, root set
  - Root set grows over time and is the seed list for any fresh crawls.
- Extracting links and normalizing relative links.
  - as it retrieves HTML pages, needs to parse out the URL links in each page it retrieves and add them to crawl list.
- Cycle avoidance
  - as it crawls it must be careful not to get stuck in a loop.
- Loops and Dups
  - loops create duplicate content.
- Trails of breadcrumbs
  - because of huge number of URLs, you need data structures to keep track which URLs youve visited.
  - ds need to be efficient in speed and memory.
  - data structures used
    - treees and hash tables
    - lossy presence bitmaps
    - chackpoints
    - partitioning
- Aliases and robot cycles
  - aliases if URL look different but point to the same resource.
- Canonicalizing URLs
  - technique to remove obvious aliases by using a standard URL form
- Filesystem link cycles
  - reate an illusion of an infinitely deep directory hierarchy where none exists.
- Dynamic virtual web spaces
  - webmasters intentionally create sophisticated crawler loops to trap innocent unsuspecting robots.
- Avoiding Loops and Dups
  - canonicalizing URLs
  - breadth-first crawling
    - across many websites first as opposed to depth first, one website
  - throttling
  - limit URL sie
  - URL/site blacklist
  - pattern detections
  - content fingerprinting
    - use content in a page to compute checksum then compare it against any incoming crawls
  - human monitoring

### Robotic HTTP
  
- crawlers too need to abide by the rules of HTTP.
- identifying request headers
  - headers they implement include
    - user-agent
    - from
    - accept
    - referer
- Virtual Hosting
  - implementers also need to support the Host header
- Conditional requests
  - re-retrieve content only when it has changed
- Response handling
  - understand status codes
  - entities
- User-agent targeting
  - optimize their content for web crawlers
- Misbehaving robots
  - runaway robots
  - stale URLs
  - long, wrong URLs
  - nosy robots
  - dynamic gateway access
- Excluding robots
  - use of a file called robots.txt, available on the server to guide the robots on where to go.
  - file format, blank lines, comment lines and rule lines.
  - HTML robot-control META tags.
    - i.e <meta name="robots" content="noindex\nofollow">
- Robot Etiquette
  - martijn koster, wrote up list of guidelines for authors of web robots
  - areas covered
    - Identification
    - Operations
    - Limit yourself
    - Tolerate loops and dups and other problems
    - Scalability
    - Reliability
    - Public relations

### Search Engines

- make heavy use of full-text indexes due to the sheer scale of computations necessary.
- a full text index takes a word and immediately tells you all documents that contain that word.
- relevancy ranking
- webmasters may spoof the search engines to aid their ranking improvement.

## HTTP-NG 

- HTTP growing pains
  - Complexity
  - Extensibility
  - Performance
  - Transport dependence
- Outline
  - Modularize and enhance
  - Modularize it into 3 layers
    - message transport layer
    - remote invocation layer
    - web application layer
    
## Identification, Authorization and Security