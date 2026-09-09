OSI Model in Depth

The OSI model—Open Systems Interconnection model—is a conceptual framework that explains how network communication works.

It divides networking into seven layers. Each layer has a specific responsibility, communicates with the layer above and below it, and usually uses protocols and data formats appropriate to its role.

7. Application
6. Presentation
5. Session
4. Transport
3. Network
2. Data Link
1. Physical

A message travels down the layers on the sender’s machine, across the network, and up the layers on the receiver’s machine.

Sender                                      Receiver

Application                                Application
Presentation                               Presentation
Session                                    Session
Transport                                  Transport
Network                                    Network
Data Link                                  Data Link
Physical  ---> network --->                Physical

────────────────────

1. Why the OSI model exists

Before layered models, networking systems were often tightly coupled. A change in one part could require changes throughout the system.

The OSI model provides:

• Separation of responsibilities
• Interoperability between vendors
• Standard terminology
• Modular protocol design
• Simpler troubleshooting
• Ability to replace one layer without redesigning everything

For example, you can replace Ethernet with Wi-Fi at Layer 2 while still using:

• IP at Layer 3
• TCP at Layer 4
• HTTP at Layer 7

Similarly, an application can use HTTPS over Wi-Fi or Ethernet without changing its application logic.

────────────────────

2. Basic idea of layering

Each layer provides services to the layer above it and uses services from the layer below it.

Application layer
      uses
Presentation layer
      uses
Session layer
      uses
Transport layer
      uses
Network layer
      uses
Data Link layer
      uses
Physical layer

A layer should generally not need to understand the internal implementation of the layers below it.

For example:

• HTTP does not need to understand electrical signaling.
• TCP does not need to know whether the packet travels over fiber or Wi-Fi.
• Ethernet does not need to understand the HTTP request inside the frame.

────────────────────

3. Encapsulation and decapsulation

Encapsulation

When sending data, each layer adds its own control information, usually in the form of a header.

Application data
      |
      v
Transport segment
      |
      v
Network packet
      |
      v
Data Link frame
      |
      v
Physical bits

More concretely:

Layer 7: HTTP data
Layer 4: TCP header + HTTP data
Layer 3: IP header + TCP segment
Layer 2: Ethernet header + IP packet + trailer
Layer 1: Electrical, optical, or radio signals

Decapsulation

At the destination, the reverse occurs:

Bits
  -> Frame
  -> Packet
  -> Segment
  -> Application data

Each receiving layer removes and interprets its own header before passing the remaining data upward.

────────────────────

4. Protocol Data Units

Each layer has a common name for the data it handles.

┌────────────┬───────────────────────────────────┐
│ Layer      │ Name of data                      │
├────────────┼───────────────────────────────────┤
│ Layers 7–5 │ Data                              │
├────────────┼───────────────────────────────────┤
│ Layer 4    │ Segment for TCP, datagram for UDP │
├────────────┼───────────────────────────────────┤
│ Layer 3    │ Packet                            │
├────────────┼───────────────────────────────────┤
│ Layer 2    │ Frame                             │
├────────────┼───────────────────────────────────┤
│ Layer 1    │ Bits or signals                   │
└────────────┴───────────────────────────────────┘

A more complete view:

Application data
TCP segment
IP packet
Ethernet frame
Physical bits

These terms are sometimes used loosely, but this distinction is useful for troubleshooting.

────────────────────

5. Layer 1: Physical layer

Purpose

The Physical layer transmits raw bits over a physical medium.

It defines things such as:

• Electrical voltage
• Light pulses
• Radio waves
• Connector types
• Cable characteristics
• Pin layouts
• Signal timing
• Bit synchronization
• Modulation
• Transmission speed
• Physical topology

Layer 1 does not understand:

• IP addresses
• MAC addresses
• TCP ports
• HTTP requests
• Files
• Users

It only transports symbols representing binary data.

1011001010010110...

Common Layer 1 media

Copper

Examples:

• Twisted-pair Ethernet
• Coaxial cable
• Telephone wiring

Characteristics include:

• Distance limitations
• Electromagnetic interference
• Signal attenuation
• Cable categories
• Duplex behavior

Fiber optic

Fiber transmits data using light.

Advantages:

• Long distance
• High bandwidth
• Resistance to electromagnetic interference
• Better security against casual electrical tapping

Types include:

• Single-mode fiber
• Multimode fiber

Wireless

Wireless networks use radio-frequency signals.

Examples:

• Wi-Fi
• Bluetooth
• Cellular networks
• Microwave links

Wireless introduces issues such as:

• Interference
• Signal strength
• Channel overlap
• Physical obstructions
• Shared medium contention
• Roaming

Layer 1 devices

• Cables
• Connectors
• Patch panels
• Repeaters
• Hubs
• Transceivers
• Antennas
• Network interface physical components

A traditional hub operates at Layer 1 because it repeats signals without understanding frames or addresses.

Layer 1 problems

Typical symptoms:

• No link light
• Interface is down
• Cable disconnected
• Bad transceiver
• Damaged fiber
• Incorrect connector
• Radio interference
• Excessive signal loss
• Speed or duplex mismatch
• Damaged network card

Useful checks:

ip link
ethtool eth0

Layer 1 question:

Can the devices physically exchange signals?

────────────────────

6. Layer 2: Data Link layer

Purpose

The Data Link layer provides communication between devices on the same local network or broadcast domain.

It handles:

• Local addressing
• Framing
• Media access control
• Error detection
• Switching
• VLANs
• Frame delivery on a local segment

The most familiar Layer 2 technologies are:

• Ethernet
• Wi-Fi
• PPP
• HDLC
• Frame Relay
• ATM, historically

MAC addresses

Ethernet and Wi-Fi use MAC addresses for local delivery.

A MAC address is typically 48 bits:

00:1A:2B:3C:4D:5E

MAC addresses identify network interfaces on a local Layer 2 network.

They are not normally used for routing across the entire internet. Routers replace the Layer 2 frame as the packet moves between networks.

Ethernet frame

A simplified Ethernet frame looks like this:

+------------+------------+------+----------------+------+
| Destination| Source MAC | Type | Payload        | FCS  |
| MAC        | MAC        |      | IP packet      |      |
+------------+------------+------+----------------+------+

Important fields include:

• Destination MAC address
• Source MAC address
• EtherType
• Payload
• Frame Check Sequence

The FCS helps detect corruption. If a frame fails the check, it is discarded.

Switches

A network switch primarily operates at Layer 2.

It learns which MAC address is reachable through which port:

MAC A -> Port 1
MAC B -> Port 2
MAC C -> Port 5

When a frame arrives, the switch:

1. Learns the source MAC address.
2. Looks up the destination MAC address.
3. Forwards the frame to the correct port.
4. Floods it if the destination is unknown.
5. May broadcast it if it is a broadcast frame.

Collision domains and broadcast domains

A switch usually gives each port its own collision domain.

A VLAN or Layer 2 broadcast domain determines where broadcast traffic travels.

Routers separate broadcast domains.

VLANs

A Virtual LAN logically separates networks on the same switching infrastructure.

Example:

VLAN 10: Engineering
VLAN 20: Finance
VLAN 30: Guest

VLAN tagging is commonly associated with IEEE 802.1Q.

A trunk link can carry traffic for multiple VLANs:

Switch A ===== trunk ===== Switch B
          VLAN 10, 20, 30

ARP

ARP, or Address Resolution Protocol, maps an IPv4 address to a MAC address on a local network.

Suppose a host wants to send to:

192.168.1.20

It broadcasts:

Who has 192.168.1.20?

The owner replies:

192.168.1.20 is at AA:BB:CC:DD:EE:FF

The host stores the result in an ARP cache.

For IPv6, neighbor discovery performs related functions using ICMPv6.

Layer 2 problems

Typical symptoms:

• Wrong VLAN
• Trunk misconfiguration
• MAC address flapping
• Switching loop
• Broadcast storm
• ARP failure
• Port security violation
• Spanning Tree issue
• Interface connected to the wrong switch port

Useful commands:

ip neigh
arp -n
bridge link
bridge fdb show
tcpdump -e

Layer 2 question:

Can devices on the same local network exchange frames?

────────────────────

7. Layer 3: Network layer

Purpose

The Network layer provides logical addressing and routing between different networks.

It handles:

• Logical addresses
• Routing
• Packet forwarding
• Path selection
• Fragmentation, in some protocols
• Time-to-live or hop limits
• Inter-network communication

The most important Layer 3 protocol is IP.

IPv4 addresses

An IPv4 address is 32 bits:

192.168.1.25

It is divided into a network portion and a host portion using a subnet mask or CIDR prefix.

Example:

192.168.1.25/24

The  /24  means the first 24 bits identify the network.

Network: 192.168.1.0/24
Usable hosts: 192.168.1.1 - 192.168.1.254
Broadcast: 192.168.1.255

IPv6 addresses

IPv6 addresses are 128 bits:

2001:db8:1234:5678::10

IPv6 provides a much larger address space and includes features such as:

• Neighbor Discovery
• Stateless Address Autoconfiguration
• Multicast
• Extension headers
• No traditional broadcast mechanism

Routers

A router connects different networks and forwards packets based on destination IP addresses.

Example:

Network A: 192.168.1.0/24
Network B: 10.0.0.0/24

Router:
  192.168.1.1 on Network A
  10.0.0.1 on Network B

If a host on Network A wants to reach a host on Network B, it sends the packet to its default gateway:

Default gateway: 192.168.1.1

Routing table

A host or router uses a routing table:

Destination       Next hop       Interface
192.168.1.0/24    directly connected eth0
10.0.0.0/24       192.168.1.1     eth0
0.0.0.0/0         ISP gateway     eth0

The default route:

0.0.0.0/0

matches destinations not covered by a more specific route.

Longest-prefix match

If several routes match, the most specific route wins.

Example:

10.0.0.0/8
10.1.0.0/16
10.1.2.0/24

For destination:

10.1.2.50

the  /24  route is selected because it is the longest, most specific match.

IP packet

A simplified IPv4 packet includes:

+----------------+----------------+
| Source IP      | Destination IP |
+----------------+----------------+
| TTL            | Protocol       |
+----------------+----------------+
| Header checksum|
+----------------+----------------+
| Payload        |
+----------------+----------------+

Important fields include:

• Source address
• Destination address
• TTL
• Protocol identifier
• Fragmentation information
• Header length
• Total length

TTL and hop limit

IPv4 uses TTL, and IPv6 uses Hop Limit.

Each router decreases the value by one. When it reaches zero, the packet is discarded.

This prevents packets from circulating forever.

 traceroute  uses this behavior to discover intermediate routers.

ICMP

ICMP supports network control and diagnostic messages.

Examples:

• Echo request and reply:  ping 
• Destination unreachable
• Time exceeded:  traceroute 
• Packet too big in IPv6

ICMP is not a transport protocol like TCP or UDP. It supports IP operation and diagnostics.

NAT

Network Address Translation modifies IP addresses, usually at a router or firewall.

A common use is translating private IPv4 addresses to one public address:

192.168.1.25:51500
        |
        v
203.0.113.10:40001

NAT helps conserve IPv4 addresses but can complicate:

• Inbound connections
• Peer-to-peer communication
• Troubleshooting
• Protocols that embed addresses in payloads

Layer 3 problems

Typical symptoms:

• No route
• Incorrect subnet mask
• Wrong default gateway
• Routing loop
• Duplicate IP address
• NAT failure
• Firewall blocking ICMP
• Packet fragmentation or MTU issue
• Asymmetric routing

Useful commands:

ip addr
ip route
ping 192.168.1.1
traceroute example.com
tracepath example.com

Layer 3 question:

Can the packet find a route from the source network to the destination network?

────────────────────

8. Layer 4: Transport layer

Purpose

The Transport layer provides end-to-end communication between applications.

It handles:

• Process-to-process delivery
• Port numbers
• Segmentation
• Reliability
• Ordering
• Flow control
• Congestion control
• Connection management
• Multiplexing

The major transport protocols are:

• TCP
• UDP
• QUIC, commonly considered transport-like even though it runs over UDP

Port numbers

Ports identify applications or services on a host.

Examples:

HTTP: 80
HTTPS: 443
DNS: 53
SSH: 22
SMTP: 25

A connection is commonly identified by a tuple:

Source IP
Source port
Destination IP
Destination port
Protocol

For TCP, a connection is often described using the four-tuple:

Client IP:client port
Server IP:server port
Protocol

TCP

TCP is connection-oriented and provides:

• Reliable delivery
• Ordered bytes
• Duplicate detection
• Retransmission
• Flow control
• Congestion control
• Full-duplex communication

TCP does not preserve application message boundaries. It provides a continuous byte stream.

If an application sends:

HELLO
WORLD

the receiver might read:

HELLOWORLD

or in smaller pieces. Applications must define their own message framing.

TCP three-way handshake

A TCP connection begins with:

Client -> Server: SYN
Server -> Client: SYN-ACK
Client -> Server: ACK

This establishes initial sequence numbers and confirms bidirectional reachability.

TCP termination

A typical graceful close uses FIN and ACK messages:

Endpoint A -> Endpoint B: FIN
Endpoint B -> Endpoint A: ACK
Endpoint B -> Endpoint A: FIN
Endpoint A -> Endpoint B: ACK

TCP can also be terminated abruptly with RST.

TCP reliability

TCP uses sequence numbers and acknowledgments.

If a segment is lost:

Segment 1 -> received
Segment 2 -> lost
Segment 3 -> received

the receiver can indicate missing data, and the sender retransmits it.

TCP flow control

The receiver advertises a receive window indicating how much data it can accept.

This prevents a fast sender from overwhelming a slow receiver.

TCP congestion control

TCP also adapts to network congestion.

It may reduce its sending rate when it detects:

• Packet loss
• Increased latency
• Duplicate acknowledgments
• Explicit congestion signals

UDP

UDP is connectionless and provides:

• Port numbers
• Datagram delivery
• Minimal overhead
• No built-in reliability
• No built-in ordering
• No built-in retransmission
• No connection handshake

UDP is useful for:

• DNS
• VoIP
• Streaming
• Online games
• DHCP
• Real-time telemetry
• Protocols that implement reliability themselves

UDP preserves datagram boundaries. If the sender sends two datagrams, the receiver receives two datagrams, assuming successful delivery.

TCP versus UDP

┌────────────────────┬─────────────────────────────────┬───────────────────────────────┐
│ Property           │ TCP                             │ UDP                           │
├────────────────────┼─────────────────────────────────┼───────────────────────────────┤
│ Connection         │ Connection-oriented             │ Connectionless                │
├────────────────────┼─────────────────────────────────┼───────────────────────────────┤
│ Reliability        │ Built in                        │ Not built in                  │
├────────────────────┼─────────────────────────────────┼───────────────────────────────┤
│ Ordering           │ Built in                        │ Not built in                  │
├────────────────────┼─────────────────────────────────┼───────────────────────────────┤
│ Retransmission     │ Built in                        │ Application responsibility    │
├────────────────────┼─────────────────────────────────┼───────────────────────────────┤
│ Flow control       │ Yes                             │ No                            │
├────────────────────┼─────────────────────────────────┼───────────────────────────────┤
│ Congestion control │ Yes                             │ No built-in general mechanism │
├────────────────────┼─────────────────────────────────┼───────────────────────────────┤
│ Data model         │ Byte stream                     │ Datagrams                     │
├────────────────────┼─────────────────────────────────┼───────────────────────────────┤
│ Overhead           │ Higher                          │ Lower                         │
├────────────────────┼─────────────────────────────────┼───────────────────────────────┤
│ Typical uses       │ HTTP, SSH, database connections │ DNS, VoIP, games, streaming   │
└────────────────────┴─────────────────────────────────┴───────────────────────────────┘

QUIC

QUIC is a modern transport protocol built over UDP.

It provides features such as:

• Reliable streams
• Encryption using TLS
• Connection migration
• Reduced handshake latency
• Multiple independent streams
• Avoidance of TCP head-of-line blocking between streams

HTTP/3 runs over QUIC.

Layer 4 problems

Typical symptoms:

• Port closed
• Connection refused
• Connection timeout
• TCP retransmissions
• UDP packets lost
• Incorrect security group rule
• Service not listening
• Ephemeral port exhaustion
• MTU-related connection problems

Useful commands:

ss -tulpen
netstat -tulpen
nc -vz example.com 443
tcpdump -nn port 443

Layer 4 question:

Can the correct application process communicate through the required port?

────────────────────

9. Layer 5: Session layer

Purpose

The Session layer manages communication sessions between applications.

It can provide:

• Session establishment
• Session maintenance
• Session termination
• Dialog control
• Checkpoints
• Recovery after interruptions
• Half-duplex or full-duplex coordination

In real-world TCP/IP systems, Session-layer responsibilities are often implemented by:

• Application protocols
• Libraries
• RPC systems
• Operating-system APIs
• TLS
• Frameworks

There is usually no universally visible “Layer 5 header” in ordinary web traffic.

Session examples

Login session

A user logs into a web application. The application maintains a session using:

• Session cookie
• Session ID
• Server-side session store
• Token

RPC session

A client establishes a logical session with a remote procedure call server.

Database session

A database client opens a connection, authenticates, runs commands, manages transactions, and closes the session.

Checkpointing

A long-running transfer might use checkpoints to resume after failure rather than restarting from the beginning.

Session layer versus transport layer

TCP manages a transport connection, but it does not understand application concepts such as:

• User login
• Transaction boundaries
• Conversation state
• Authentication state
• Application-level reconnects

Those are typically Session-layer or Application-layer concerns.

Layer 5 question

Is the logical conversation between the applications established and maintained?

────────────────────

10. Layer 6: Presentation layer

Purpose

The Presentation layer translates data between application representation and network representation.

It handles:

• Data encoding
• Character sets
• Serialization
• Compression
• Encryption and decryption
• Data format conversion
• Syntax negotiation

Character encoding

Text can be represented using:

• ASCII
• UTF-8
• UTF-16
• ISO-8859-1

For example, the same visible character can have different binary representations depending on encoding.

Serialization

Applications convert structured data into transferable formats.

Examples:

• JSON
• XML
• Protocol Buffers
• MessagePack
• ASN.1
• CSV

Example JSON:

{
  "user": "alice",
  "active": true
}

The Presentation layer conceptually concerns how this structure is represented and interpreted.

Compression

Compression reduces data size.

Examples:

• gzip
• Brotli
• zstd
• Deflate

HTTP content encoding can use compression:

Content-Encoding: gzip

Encryption

Encryption transforms readable data into ciphertext.

Examples:

• TLS encryption for HTTPS
• Data encryption formats
• Application-level encryption
• Encoded certificate formats

Strictly speaking, TLS is not a perfect fit into one OSI layer. It spans responsibilities commonly associated with the Session, Presentation, and Transport-adjacent areas.

Encoding versus encryption

These are different:

Encoding

Designed for compatibility or representation.

"hello" -> Base64 -> "aGVsbG8="

Base64 is not security.

Encryption

Designed to provide confidentiality and requires a key.

plaintext + key -> ciphertext

Layer 6 question

Can both applications interpret the data in the same format, encoding, compression, and security context?

────────────────────

11. Layer 7: Application layer

Purpose

The Application layer provides network services directly to applications and users.

It defines:

• Application commands
• Request and response structures
• Resource naming
• Authentication behavior
• Error messages
• Business-level semantics
• Content types
• Application-specific state

The Application layer is not the user interface itself. It is the network protocol used by applications.

Common Layer 7 protocols

HTTP and HTTPS

Used for websites, APIs, and web services.

Example:

GET /users/42 HTTP/1.1
Host: example.com
Accept: application/json

HTTP defines:

• Methods
• URLs
• Headers
• Status codes
• Request and response bodies
• Caching
• Cookies
• Content negotiation

DNS

Maps names to records.

example.com -> 203.0.113.20

DNS records include:

• A
• AAAA
• CNAME
• MX
• NS
• TXT
• SRV
• SOA

DHCP

Automatically provides network configuration such as:

• IP address
• Subnet mask
• Default gateway
• DNS server

A common IPv4 exchange is:

Discover
Offer
Request
Acknowledgment

SMTP

Used for sending email between mail systems.

IMAP and POP3

Used for retrieving email.

SSH

Provides secure remote shell access and secure channels.

FTP and SFTP

FTP transfers files but is not secure by default. SFTP runs over SSH and is a different protocol.

SNMP

Used to monitor and manage network devices.

NTP

Synchronizes clocks.

LDAP

Provides directory services and identity lookup.

MQTT

A lightweight publish/subscribe protocol commonly used in IoT.

SMB and NFS

Used for network file sharing.

Layer 7 question

Does the application protocol provide the expected service and understand the request?

────────────────────

12. A complete example: opening a website

Suppose you visit:

https://example.com

Step 1: Application layer

The browser needs the IP address for  example.com .

It sends a DNS query.

Step 2: Transport layer

The DNS query may use UDP port 53, TCP port 53, or encrypted DNS transport depending on configuration.

Step 3: Network layer

The query is placed inside an IP packet.

The source and destination IP addresses are added.

Step 4: Data Link layer

The IP packet is placed inside an Ethernet or Wi-Fi frame.

The local gateway’s MAC address may be determined using ARP or IPv6 Neighbor Discovery.

Step 5: Physical layer

The frame becomes electrical signals, light pulses, or radio transmissions.

Step 6: DNS response

The browser receives the IP address.

Step 7: HTTPS connection

The browser connects to the server, commonly using TCP port 443 or QUIC for HTTP/3.

Step 8: TLS handshake

The browser and server negotiate encryption and authenticate the server certificate.

Step 9: HTTP request

The browser sends something like:

GET / HTTP/2
Host: example.com

Step 10: Server response

The server returns:

HTTP/2 200
Content-Type: text/html

The browser then requests additional resources such as:

• CSS
• JavaScript
• Images
• Fonts
• API data

────────────────────

13. Where headers are added

A simplified packet may look like this:

+-----------------------------+
| Ethernet header             | Layer 2
+-----------------------------+
| IP header                   | Layer 3
+-----------------------------+
| TCP header                  | Layer 4
+-----------------------------+
| TLS or HTTP data            | Layers 5-7
+-----------------------------+
| Ethernet trailer            | Layer 2
+-----------------------------+

Each router generally:

1. Removes the incoming Layer 2 frame.
2. Examines the Layer 3 packet.
3. Decreases TTL or Hop Limit.
4. Determines the next hop.
5. Creates a new Layer 2 frame for the next link.

The IP packet may remain mostly the same while the Layer 2 frame changes at every hop.

────────────────────

14. Addressing at each layer

Different layers use different identifiers.

┌─────────┬───────────────────────────┬───────────────────────────┐
│ Layer   │ Identifier                │ Example                   │
├─────────┼───────────────────────────┼───────────────────────────┤
│ Layer 2 │ MAC address               │ 00:11:22:33:44:55         │
├─────────┼───────────────────────────┼───────────────────────────┤
│ Layer 3 │ IP address                │ 192.168.1.10              │
├─────────┼───────────────────────────┼───────────────────────────┤
│ Layer 4 │ Port number               │ 443                       │
├─────────┼───────────────────────────┼───────────────────────────┤
│ Layer 7 │ Application resource/name │ https://example.com/users │
└─────────┴───────────────────────────┴───────────────────────────┘

These answer different questions:

• MAC address: Which local network interface?
• IP address: Which host or network?
• Port: Which process or service?
• Application identifier: Which resource or operation?

────────────────────

15. Network devices by OSI layer

┌───────────────────────┬───────────────────────────────┐
│ Device                │ Primary layer                 │
├───────────────────────┼───────────────────────────────┤
│ Cable, repeater, hub  │ Layer 1                       │
├───────────────────────┼───────────────────────────────┤
│ Bridge, switch        │ Layer 2                       │
├───────────────────────┼───────────────────────────────┤
│ Router                │ Layer 3                       │
├───────────────────────┼───────────────────────────────┤
│ Layer 3 switch        │ Layers 2–3                    │
├───────────────────────┼───────────────────────────────┤
│ Firewall              │ Layers 3–7, depending on type │
├───────────────────────┼───────────────────────────────┤
│ NAT gateway           │ Layer 3/4 behavior            │
├───────────────────────┼───────────────────────────────┤
│ Load balancer         │ Layers 4 or 7                 │
├───────────────────────┼───────────────────────────────┤
│ Proxy server          │ Layer 7                       │
├───────────────────────┼───────────────────────────────┤
│ Wireless access point │ Layers 1–2                    │
├───────────────────────┼───────────────────────────────┤
│ IDS/IPS               │ Often Layers 3–7              │
├───────────────────────┼───────────────────────────────┤
│ DNS server            │ Layer 7                       │
├───────────────────────┼───────────────────────────────┤
│ Web server            │ Layer 7                       │
└───────────────────────┴───────────────────────────────┘

Many modern devices operate across multiple layers.

For example, an application load balancer understands:

• IP addresses
• TCP connections
• TLS
• HTTP paths
• Host headers
• Cookies

────────────────────

16. OSI model versus TCP/IP model

The OSI model has seven layers. The TCP/IP model is more practical and commonly used in real networks.

TCP/IP four-layer model

┌────────────────────────┬──────────────────────────┐
│ TCP/IP layer           │ Corresponding OSI layers │
├────────────────────────┼──────────────────────────┤
│ Application            │ Layers 5, 6, 7           │
├────────────────────────┼──────────────────────────┤
│ Transport              │ Layer 4                  │
├────────────────────────┼──────────────────────────┤
│ Internet               │ Layer 3                  │
├────────────────────────┼──────────────────────────┤
│ Network Access or Link │ Layers 1 and 2           │
└────────────────────────┴──────────────────────────┘

Some versions use five layers by separating Physical and Data Link:

Application
Transport
Network
Data l

Key difference

The OSI model is primarily a conceptual reference model.

The TCP/IP model is based on the protocols that power the internet:

• IP
• TCP
• UDP
• DNS
• HTTP
• Ethernet
• Wi-Fi

In everyday engineering, people often use “Layer 2,” “Layer 3,” and “Layer 7” as shorthand even when discussing TCP/IP networks.

────────────────────

17. OSI troubleshooting methodology

A useful troubleshooting process moves from the bottom upward.

Layer 1: Physical

Check:

• Cable
• Link light
• Power
• Interface state
• Wireless signal
• Transceiver
• Speed and duplex

Questions:

Is the cable connected?
Is the interface up?
Is the radio signal usable?

Layer 2: Data Link

Check:

• VLAN
• Switch port
• MAC table
• ARP or Neighbor Discovery
• Spanning Tree
• Port security

Questions:

Can the host reach another device on the same subnet?
Is it in the correct VLAN?

Layer 3: Network

Check:

• IP address
k
• Default gateway
• Routing table
• NAT
• ACLs
• MTU

Questions:

Can the host reach its gateway?
Can it reach a remote IP address?

Layer 4: Transport

Check:

• Listening ports
• Firewall rules
• Security groups
• TCP handshake
• UDP delivery
• Connection resets
• Retransmissions

Questions:

Is the service listening?
Is the port reachable?

Layers 5–7: Session, Presentation, Application

Check:

• TLS certificate
• Authentication
• Session expiration
• Serialization foat
• HTTP status
• Application logs
• API route
• Request headers
• Database dependencies

Questions:

Can the client establish the session?
Can it decrypt and parse the response?
Does the application understand the request?

────────────────────

18. Example troubleshooting with  curl 

Suppose HTTPS is not working.

curl -v https://example.com

This can reveal several layers:

• DNS resolution
• TCP connection
• TLS handshake
• Certificate verifiP request
• HTTP response

Typical output stages correspond roughly to:

DNS resolution       Layer 7 support
TCP connection       Layer 4
TLS handshake        Layers 5–6 conceptually
HTTP request         Layer 7

Other useful tools:

ping example.com
traceroute example.com
dig example.com
ip addr
ip route
ss -tulpen
tcpdump -nn

────────────────────

19. Common failures mapped to layers

┌────────────────────────┬────────────────────────┐
│ Symptom                │ Likely layer           │
├────────────────────────┼────────────────────────┤
│ No link light          │ Layer 1                │
├────────────────────────┼────────────────────────┤
│ Bad cable              │ Layer 1                │
├────────────────────────┼────────────────────────┤
│ Wrong Wi-Fi channel    │ Layer 1/2              │
├────────────────────────┼────────────────────────┤
│ Wrong VLAN             │ Layer 2                │
├────────────────────┼────────────────────────┤
│ ARP failure            │ Layer 2                │
├────────────────────────┼────────────────────────┤
│ Duplicate IP           │ Layer 2/3              │
├────────────────────────┼───────────────────â─┤
│ Missing route          │ Layer 3                │
├────────────────────────┼────────────────────────┤
│ Wrong gateway          │ Layer 3                │
├────────────────────────┼────────────────────────┤
│ TTL expired            │ Layer 3                │
├────────────────────────┼────────────────────────┤
│ Port closed            │ Layer 4                │
├────────────────────────┼────────────────────────┤
│ Connection timeout     │ Layers 3–4             │
├────────────────────────â─────────┤
│ TCP reset              │ Layer 4 or application │
├────────────────────────┼────────────────────────┤
│ TLS certificate error  │ Layers 5–6             │
├────────────────────────┼────────────────────────┤
│ Invalid JSON           │ Layer 6/7            ├────────────────────────┼────────────────────────┤
│ HTTP 404               │ Layer 7                │
├────────────────────────┼────────────────────────┤
│ Authentication failure │ Layer 7                │
├────────────────────────┼───â─────────────────┤
│ Application crash      │ Layer 7                │
└────────────────────────┴────────────────────────┘

The mapping is not absolute because modern protocols cross traditional OSI boundaries.

────────────────────

20. Security by OSI layer

Layer 1 security

Threats:

• Cable tapping
• Signal intercess jamming
• Physical device theft
• Unauthorized port access

Controls:

• Physical access control
• Cable security
• Shielding
• Wireless encryption
• Secure facilities

Layer 2 security

Threats:

• MAC spoofing
• ARP spoofing
• VLAN hopping
• Switching loops
• Rogue DHCP servers
• Broadcast attacks

Controls:

• Port security
• DHCP snooping
• Dynamic ARP Inspection
• 802.1X
• VLAN segmentation
• BPDU Guard
• Private VLANs

Layer 3 security

Threats:

• IP spoofing attacks
• Unauthorized network access
• ICMP abuse
• Route manipulation

Controls:

• Firewalls
• Network ACLs
• Secure routing protocols
• IPsec
• Filtering
• Network segmentation

Layer 4 security

Threats:

• Port scanning
• SYN floods
• UDP floods
• Connection exhaustion
• TCP reset attacks

Controls:

• Stateful firewalls
• Rate limiting
• SYN cookies
• DDoS protection
• Network security groups

Layers 5–7 security

Threats:

• Session hijacking
• CredentWeak encryption
• Injection
• Cross-site scripting
• Broken authentication
• Malformed input
• API abuse

Controls:

• TLS
• Secure cookies
• Token expiration
• Input validation
• Authentication
• Authorization
• Web application firewalls
• Secure serialization
• Application logging

────────────────────

21. Important OSI model limitations

The OSI model is extremely useful, but it is not a perfect description of modern networks.

Layers aot strictly separated

Real protocols can span multiple layers.

Examples:

• TLS involves encryption, session negotiation, and transport interaction.
• QUIC combines transport and security features over UDP.
• HTTP/3 uses QUIC rather than TCP.
• Firewalls may inspect multiple layers.
• Load balancers may operate at Layer 4 or Layer 7.

Session and Presentation layers are often not visible

The TCP/IP stack commonly combines Layers 5, 6, and 7 into one Application layer.

Some protocols do not fitxamples:

• ARP sits between Layer 2 and Layer 3.
• ICMP is carried by IP but supports network control.
• DNS uses both UDP and TCP.
• TLS sits above TCP but below HTTP.
• VPN protocols may encapsulate several layers.

“Layer 7” does not mean only applications

A device called a Layer 7 firewall or load balancer usually means it understands application protocol information such as:

• HTTP method
• URL
• Host header
• Cookies
• TLS SNI
• API route

────────────────────

22. A memory aid

From Layer 7 down to Layer 1:

All People Seem To Need Data Processing

• Application
• Presentation
• Session
• Transport
• Network
• Data Link
• Physical

From Layer 1 up to Layer 7:

Please Do Not Throw Sausage Pizza Away

• Physical
• Data Link
• Network
• Transport
• Session
• Presentation
• Application

────────────────────

23. The complete mental model

When an application sends data:

ation:
  "GET /index.html"

Presentation:
  Encode, compress, or encrypt the data

Session:
  Maintain the logical conversation

Transport:
  Use TCP/UDP/QUIC and a port

Network:
  Add source and destination IP addresses

Data Link:
  Add source and destination MAC addresses

Physical:
  Transmit bits over cable, fiber, or radio

At the receiver:

Physical:
  Receive signals

Data Link:
  Validate and remove the frame

Network:
  Validate and route the IP packet

Transport:
  Reassemble and deliver to the correct port

Session:
  Restore conversation state

Presentation:
  Decrypt, decompress, and decode

Application:
  Process the request

The most important practical idea is:

Layer 2 delivers locally, Layer 3 delivers between networks, Layer 4 delivers to processes, and Layer 7 provides application meaning.

Understanding that distinction makes it much easier to design networks, diagnose failures, configure firewalls, understand cloud networking, and reason about protocols.
