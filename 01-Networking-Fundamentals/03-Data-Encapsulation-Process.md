

# 1. What Is Data Encapsulation?

**Data encapsulation** is the process where each networking layer adds its own control information to data before sending it across the network.

Simple idea:

```text
Application Data
      ↓
   + Header
      ↓
  Segment
      ↓
   + Header
      ↓
   Packet
      ↓
   + Header
      ↓
   Frame
      ↓
     Bits
```

At the receiving device, the reverse process happens:

```text
Bits
 ↓
Frame
 ↓
Packet
 ↓
Segment
 ↓
Application Data
```

This reverse process is called **decapsulation**.

---

# 2. Why Do We Need Encapsulation?

Different networking layers have different responsibilities.

For example:

* Application layer → What data is being exchanged?
* Transport layer → Which application should receive it?
* Network layer → Which destination network/device?
* Data Link layer → Which device on the local network?
* Physical layer → How are bits transmitted?

Encapsulation allows these responsibilities to work together.

---

# 3. Real-World Example

Suppose you open:

```text
https://example.com
```

Your browser creates HTTP/HTTPS-related application data.

That data travels through several layers.

```text
Browser
  ↓
HTTP/HTTPS
  ↓
TCP
  ↓
IP
  ↓
Ethernet/Wi-Fi
  ↓
Physical medium
  ↓
Internet
```

Each layer adds information required by the next stage.

---

# 4. Encapsulation in the TCP/IP Model

The TCP/IP model is commonly represented using four layers:

| TCP/IP Layer   | Example Protocol | Data Unit          |
| -------------- | ---------------- | ------------------ |
| Application    | HTTP, DNS, SSH   | Data               |
| Transport      | TCP, UDP         | Segment / Datagram |
| Internet       | IPv4, IPv6       | Packet             |
| Network Access | Ethernet, Wi-Fi  | Frame / Bits       |

> Terminology can vary between textbooks. In particular, **TCP uses "segment"**, while **UDP uses "datagram."**

---

# 5. Step-by-Step Encapsulation

Let's imagine:

```text
PC-A → Web Server
```

PC-A wants to send:

```text
GET / HTTP/1.1
```

---

## Step 1 — Application Layer

The application creates the actual data.

Example:

```text
HTTP Request
```

At this stage, think:

```text
DATA
```

The application doesn't need to know how Ethernet frames are transmitted.

### VAPT relevance

This is where technologies such as:

* HTTP
* DNS
* SSH
* FTP
* SMTP

operate.

Many web vulnerabilities occur at or above this level.

---

# 6. Step 2 — Transport Layer

TCP or UDP handles transport.

Suppose HTTP uses TCP.

TCP adds information such as:

```text
Source Port
Destination Port
Sequence Number
Acknowledgment Number
Flags
Window
Checksum
```

Conceptually:

```text
TCP Header + Application Data
```

This creates a:

# TCP Segment

Example:

```text
Source Port:      51524
Destination Port: 443
```

The destination port helps the receiving system identify the intended service/application.

### VAPT relevance

Ports are extremely important during enumeration.

Example:

```text
22  → SSH
80  → HTTP
443 → HTTPS
```

---

# 7. Step 3 — Internet Layer

The IP layer adds an IP header.

Conceptually:

```text
IP Header
   +
TCP Segment
```

This creates an:

# IP Packet

The IP header contains information such as:

```text
Source IP
Destination IP
TTL / Hop Limit
Protocol / Next Header
```

Example:

```text
Source IP:      192.168.1.10
Destination IP: 93.184.216.34
```

The destination IP tells routers where the packet should go.

---

# 8. Step 4 — Network Access Layer

The packet is placed inside a network frame.

For Ethernet, conceptually:

```text
Ethernet Header
       +
   IP Packet
       +
Ethernet Trailer
```

This creates an:

# Ethernet Frame

The frame can contain information such as:

```text
Source MAC
Destination MAC
EtherType
Payload
FCS
```

Example:

```text
Source MAC:      AA:AA:AA:AA:AA:AA
Destination MAC: BB:BB:BB:BB:BB:BB
```

---

# 9. Step 5 — Physical Transmission

The frame is ultimately transmitted as signals representing bits.

Conceptually:

```text
101010101010101010...
```

These signals can travel through:

* Ethernet cable
* Fiber
* Wi-Fi radio
* Other physical media

---

# 10. Complete Encapsulation Diagram

This is the most important diagram to remember:

```text
APPLICATION
┌───────────────────────────────┐
│ HTTP Data                     │
└───────────────────────────────┘
                ↓
TRANSPORT
┌──────────────┬───────────────┐
│ TCP Header   │ HTTP Data     │
└──────────────┴───────────────┘
                ↓
INTERNET
┌────────────┬─────────────────┐
│ IP Header  │ TCP Segment     │
└────────────┴─────────────────┘
                ↓
NETWORK ACCESS
┌────────────┬───────────────┬───────┐
│ ETH Header │ IP Packet     │ FCS   │
└────────────┴───────────────┴───────┘
                ↓
PHYSICAL
        0101010101010101...
```

---

# 11. PDU Names

A **PDU (Protocol Data Unit)** is the data format used at a particular layer.

Remember:

```text
Application → Data
Transport   → Segment / Datagram
Internet    → Packet
Data Link   → Frame
Physical    → Bits
```

### Important

Don't blindly say:

> "Every transport-layer packet is called a segment."

More accurately:

```text
TCP → Segment
UDP → Datagram
```

---

# 12. Encapsulation vs Decapsulation

## Encapsulation

Sender adds information:

```text
Data
 ↓
Segment
 ↓
Packet
 ↓
Frame
 ↓
Bits
```

## Decapsulation

Receiver removes information:

```text
Bits
 ↓
Frame
 ↓
Packet
 ↓
Segment
 ↓
Data
```

---

# 13. Receiver-Side Decapsulation

Suppose the server receives an Ethernet frame.

### Layer 1

Receives physical signals/bits.

↓

### Layer 2

Ethernet processes the frame.

It checks things such as:

```text
Destination MAC
FCS
EtherType
```

↓

### Layer 3

IP processes the packet.

It checks:

```text
Destination IP
Protocol information
TTL/Hop Limit
```

↓

### Layer 4

TCP processes the segment.

It uses:

```text
Destination Port
Sequence Number
Flags
```

↓

### Layer 7

The application receives the actual data.

```text
HTTP Request
```

---

# 14. Important: Headers Are Not All the Same

Each protocol has its own header.

Example:

```text
Ethernet Header
       ↓
IP Header
       ↓
TCP Header
       ↓
HTTP Data
```

Different headers answer different questions.

| Header   | Main purpose                              |
| -------- | ----------------------------------------- |
| Ethernet | Local network delivery                    |
| IP       | Network-to-network delivery               |
| TCP      | Reliable transport + application endpoint |
| HTTP     | Application communication                 |

---

# 15. MAC vs IP vs Port

This is extremely important for networking and VAPT.

### MAC Address

Used primarily for **local Layer-2 delivery**.

Example:

```text
AA:BB:CC:DD:EE:FF
```

### IP Address

Used for **Layer-3 addressing/routing**.

Example:

```text
192.168.1.20
```

### Port

Used by TCP/UDP to identify a **service/application endpoint**.

Example:

```text
443
```

Think:

```text
MAC  → Local device
IP   → Network destination
Port → Service/application
```

---

# 16. What Happens When a Packet Crosses a Router?

This is an important interview concept.

Suppose:

```text
PC
 ↓
Router
 ↓
Internet
 ↓
Server
```

At the router:

```text
Incoming Ethernet Frame
          ↓
     Remove L2 header
          ↓
       Inspect IP
          ↓
      Route packet
          ↓
 Create new L2 frame
          ↓
      Next network
```

The **Layer-2 frame is normally replaced at each routed hop**.

The IP packet is forwarded toward its destination.

---

# 17. MAC Address Does NOT Normally Travel End-to-End

Beginner mistake:

> "The source PC's MAC address goes all the way to the web server."

Usually, **no**.

Example:

```text
PC ─── Router ─── Router ─── Server
```

Each local network segment has its own Layer-2 frame.

Conceptually:

```text
PC → Router

MAC A → MAC B
```

Then:

```text
Router → Next Hop

MAC C → MAC D
```

And later:

```text
Next Hop → Server

MAC E → MAC F
```

The Layer-2 addresses change hop-by-hop.

---

# 18. What About IP Addresses?

Normally, the source and destination IP addresses remain associated with the packet across routed hops.

However, there are important exceptions/changes.

For example:

### NAT

A NAT device can translate addresses.

```text
Private IP
192.168.1.10
     ↓
NAT Router
     ↓
Public IP
203.0.113.x
```

So don't memorize:

> "IP addresses never change."

Instead remember:

> **Routing normally forwards the IP packet, while NAT can translate IP addresses.**

---

# 19. TTL and Hop Limit

IPv4 uses:

```text
TTL
```

IPv6 uses:

```text
Hop Limit
```

They help prevent packets from circulating forever.

Each router decreases the IPv4 TTL by at least 1.

Example:

```text
TTL 64
 ↓ Router
TTL 63
 ↓ Router
TTL 62
 ↓ Router
...
```

When the value reaches zero, the packet is discarded.

### VAPT relevance

TTL behavior can help with:

* Network troubleshooting
* Path analysis
* OS/network fingerprinting clues

But TTL alone should **not** be treated as definitive OS identification.

---

# 20. Encapsulation Example: HTTPS

Suppose you visit:

```text
https://example.com
```

Conceptually:

```text
Application
    ↓
HTTP
    ↓
TLS encryption
    ↓
TCP
    ↓
IP
    ↓
Ethernet/Wi-Fi
```

The exact protocol stack can vary with modern protocols such as HTTP/3.

For HTTP/3:

```text
HTTP
 ↓
QUIC
 ↓
UDP
 ↓
IP
 ↓
Ethernet/Wi-Fi
```

This is an important modern networking detail.

---

# 21. TCP vs UDP Encapsulation

## TCP

```text
Application Data
      ↓
TCP Header + Data
      ↓
IP Header + TCP Segment
      ↓
Frame
```

## UDP

```text
Application Data
      ↓
UDP Header + Data
      ↓
IP Header + UDP Datagram
      ↓
Frame
```

---

# 22. VAPT Perspective

Understanding encapsulation helps you understand what security tools actually observe.

For example:

```text
Nmap
 ↓
Network communication
 ↓
TCP/IP
 ↓
Services
```

Wireshark can show multiple protocol layers:

```text
Ethernet
  └── IP
       └── TCP
            └── HTTP
```

This helps a pentester understand:

* Where traffic is going
* Which IP is communicating
* Which ports are involved
* Which protocol is being used
* Whether TCP flags look expected
* Where packets are being dropped
* Whether segmentation exists
* How security controls affect traffic

---

# 23. Encapsulation in Wireshark

When analyzing a packet, you may see something similar to:

```text
Frame
 └── Ethernet II
      └── Internet Protocol
           └── Transmission Control Protocol
                └── Hypertext Transfer Protocol
```

This is basically the encapsulation structure displayed visually.

### Practical lab

Use an **authorized lab** and capture traffic with Wireshark.

Then inspect:

```text
Ethernet
→ IP
→ TCP
→ Application protocol
```

Try to identify:

* Source MAC
* Destination MAC
* Source IP
* Destination IP
* Source port
* Destination port
* TCP flags

---

# 24. TCP Flags and Encapsulation

TCP includes flags such as:

```text
SYN
ACK
FIN
RST
PSH
URG
```

Example TCP connection:

```text
Client                  Server

  SYN  ────────────────→

       ←──────── SYN-ACK

  ACK  ────────────────→
```

The TCP segment itself is encapsulated inside an IP packet and then inside a Layer-2 frame.

---

# 25. VAPT Example: Port Scanning

When a scanner checks a TCP port, it is interacting with the networking stack.

Conceptually:

```text
Scanner
  ↓
TCP packet/segment
  ↓
IP packet
  ↓
Ethernet/Wi-Fi frame
  ↓
Network
  ↓
Target
```

A response can tell you something about the target's network/service behavior.

For example:

```text
SYN → Target
     ↓
SYN-ACK → Port may be listening
```

or:

```text
SYN → Target
     ↓
RST → Port likely closed
```

Actual results can also be affected by firewalls, filtering, IDS/IPS, routing, and network conditions.

---

# 26. Firewall and Encapsulation

A firewall can inspect different layers depending on its capabilities.

Conceptually:

```text
Packet arrives
      ↓
Firewall
      ↓
Inspect:
IP?
Port?
Protocol?
Connection state?
Application data?
      ↓
ALLOW / DROP / REJECT
```

This is why understanding layers is useful during VAPT.

---

# 27. Common Beginner Mistakes

### ❌ Mistake 1

> IP address = device identity at every layer.

Not exactly.

IP is a Layer-3 address used for logical addressing/routing.

---

### ❌ Mistake 2

> MAC addresses travel across the Internet unchanged.

No.

Layer-2 addressing is normally local to a network segment.

---

### ❌ Mistake 3

> TCP is Layer 3.

No.

TCP belongs to the **Transport layer**.

---

### ❌ Mistake 4

> HTTP is responsible for IP routing.

No.

HTTP is an application-layer protocol.

---

### ❌ Mistake 5

> Every packet contains a TCP header.

No.

Packets can carry TCP, UDP, ICMP and other protocols.

---

### ❌ Mistake 6

> UDP is simply "TCP but faster."

Incorrect.

UDP and TCP provide different transport semantics and features.

---

# 28. Interview Questions

### Q1. What is encapsulation?

The process of adding protocol-specific control information as data moves down the networking stack.

### Q2. What is decapsulation?

The reverse process where protocol information is processed/removed as data moves up the stack.

### Q3. What is a PDU?

Protocol Data Unit—the form of data at a particular networking layer.

### Q4. What is the PDU of TCP?

**Segment.**

### Q5. What is the PDU of UDP?

**Datagram.**

### Q6. What is the PDU at the IP layer?

**Packet.**

### Q7. What is the PDU at Ethernet Layer 2?

**Frame.**

### Q8. What is the purpose of a port?

It identifies a transport-layer endpoint associated with a service/application.

### Q9. Does a MAC address normally remain unchanged across routers?

No. Layer-2 addressing is normally rewritten for each routed hop.

### Q10. What does TTL do?

It limits how many routing hops an IPv4 packet can make.

---

# 29. Scenario-Based Questions

### Scenario 1

You capture:

```text
Ethernet → IPv4 → TCP → HTTP
```

What is the encapsulation order?

**Answer:**

```text
HTTP
 ↓
TCP
 ↓
IPv4
 ↓
Ethernet
```

---

### Scenario 2

You see:

```text
Destination Port = 443
```

What does this tell you?

It indicates the transport-layer destination endpoint is port 443, commonly associated with HTTPS.

It does **not** prove that HTTPS is actually running there.

---

### Scenario 3

A packet crosses three routers.

Does its Ethernet destination MAC remain the same?

**Answer:** No. The Layer-2 frame is normally rebuilt for each hop.

---

### Scenario 4

A TCP SYN receives SYN-ACK.

What does that generally indicate?

The destination TCP port is reachable and appears to be accepting the connection, although filtering and other network conditions can affect interpretation.

---

# 30. MCQs

### Q1. What is added at the transport layer?

A. MAC address
B. TCP/UDP information
C. Ethernet FCS only
D. DNS record

**Answer: B**

---

### Q2. TCP data is commonly called:

A. Frame
B. Datagram
C. Segment
D. Bit

**Answer: C**

---

### Q3. IP operates primarily at:

A. Application
B. Transport
C. Internet/Network
D. Physical

**Answer: C**

---

### Q4. Ethernet creates a:

A. Segment
B. Frame
C. Packet
D. Datagram

**Answer: B**

---

### Q5. What is the reverse of encapsulation?

A. Routing
B. Switching
C. Decapsulation
D. Fragmentation

**Answer: C**

---

### Q6. Which identifies a transport-layer endpoint?

A. MAC address
B. Port number
C. Ethernet FCS
D. TTL

**Answer: B**

---

### Q7. IPv4 uses:

A. Hop Limit
B. TTL
C. Sequence Number
D. FCS

**Answer: B**

---

### Q8. Which is normally rewritten at every routed hop?

A. Layer-2 addressing
B. Destination application data
C. TCP destination port
D. HTTP method

**Answer: A**

---

### Q9. UDP's PDU is commonly called:

A. Frame
B. Packet
C. Datagram
D. Segment

**Answer: C**

---

### Q10. Which tool is commonly used to inspect packet encapsulation?

A. Wireshark
B. Notepad
C. Calculator
D. Paint

**Answer: A**

---

# 31. Must Remember ⭐

```text
APPLICATION
    ↓
DATA

TRANSPORT
    ↓
TCP → SEGMENT
UDP → DATAGRAM

INTERNET
    ↓
IP → PACKET

NETWORK ACCESS
    ↓
Ethernet/Wi-Fi → FRAME

PHYSICAL
    ↓
BITS
```

### Golden Rule

```text
Sender:
Data → Segment → Packet → Frame → Bits

Receiver:
Bits → Frame → Packet → Segment → Data
```

---

# 32. Quick Cheat Sheet

| Concept       | Remember                          |
| ------------- | --------------------------------- |
| Encapsulation | Add layer-specific information    |
| Decapsulation | Process/remove layer information  |
| TCP           | Segment                           |
| UDP           | Datagram                          |
| IP            | Packet                            |
| Ethernet      | Frame                             |
| Physical      | Bits/signals                      |
| MAC           | Local Layer-2 addressing          |
| IP            | Logical Layer-3 addressing        |
| Port          | Transport endpoint                |
| TTL           | IPv4 hop limit                    |
| Hop Limit     | IPv6 equivalent                   |
| Router        | Forwards packets between networks |
| Wireshark     | Packet analysis                   |
| Nmap          | Network/service discovery         |

---

# 33. Practical Lab Ideas 🧪

Only perform these against systems you own or are explicitly authorized to test.

### Lab 1 — Wireshark Encapsulation

Capture traffic from your own machine and identify:

```text
Ethernet
 ↓
IP
 ↓
TCP/UDP
 ↓
Application protocol
```

### Lab 2 — TCP Handshake

Capture a TCP connection and identify:

```text
SYN
SYN-ACK
ACK
```

### Lab 3 — Local Web Server

Run a simple web server in your own lab:

```text
Browser
 ↓
HTTP
 ↓
TCP
 ↓
IP
 ↓
Ethernet
```

Then inspect the traffic in Wireshark.

### Lab 4 — Compare TCP and UDP

Generate authorized local TCP and UDP traffic and compare their headers in Wireshark.

---

# 34. Final Mental Model 🧠

Imagine sending a parcel.

```text
Application
"What am I sending?"

        ↓

TCP/UDP
"Which application should receive it?"

        ↓

IP
"Which network/device should receive it?"

        ↓

Ethernet/Wi-Fi
"Which local device is the next hop?"

        ↓

Physical
"How do I transmit the bits?"
```

That's **data encapsulation**.

The most important idea is not memorizing the names—it is understanding **why each layer adds its information and how that information is used when the data reaches the next device.**

---

## Key Takeaways

1. Encapsulation happens as data moves **down** the networking stack.
2. Decapsulation happens as received data is processed **up** the stack.
3. TCP → **Segment**.
4. UDP → **Datagram**.
5. IP → **Packet**.
6. Ethernet/Wi-Fi → **Frame**.
7. Physical transmission → **Bits/signals**.
8. MAC addresses are primarily relevant to **local Layer-2 delivery**.
9. IP addresses are used for **logical addressing/routing**.
10. Ports identify **transport-layer endpoints**.
11. Routers normally replace the Layer-2 frame for the next network.
12. Understanding encapsulation is essential for **Wireshark, Nmap, network troubleshooting and VAPT**.



**Why not 10/10?** Networking has many implementation-specific details (NAT, tunneling, VLANs, VPNs, fragmentation, IPv6 extension headers, QUIC/HTTP-3, etc.) that are intentionally kept at an introductory/intermediate level here. Adding all of them would make this particular file less beginner-friendly.
