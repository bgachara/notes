# High Performance Browser Networking


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
       