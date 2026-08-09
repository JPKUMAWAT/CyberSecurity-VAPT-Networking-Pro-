# TCP/IP Model Explained

> **Networking Fundamentals — 02**

## 1. What Is the TCP/IP Model?

The **TCP/IP Model** is a practical networking model that explains how devices communicate over a network and the Internet.

TCP/IP stands for:

* **TCP** — Transmission Control Protocol
* **IP** — Internet Protocol

Unlike the OSI model, which has **7 layers**, the commonly used TCP/IP model has **4 layers**:

```text
┌──────────────────────────────┐
│  4. Application Layer        │
├──────────────────────────────┤
│  3. Transport Layer          │
├──────────────────────────────┤
│  2. Internet Layer           │
├──────────────────────────────┤
│  1. Network Access Layer     │
└──────────────────────────────┘
```

### Simple idea

When you open a website, different layers perform different jobs:

```text
Application → What data do I want?
Transport   → How should the data be delivered?
Internet    → Where should it go?
Network     → How should it travel over the local network?
```

---

# 2. Why Do We Need the TCP/IP Model?

A network is not just "computer A sends data to computer B."

Many things must happen:

1. Application creates data.
2. Data is prepared for transport.
3. Source and destination IP addresses are used.
4. Data is placed into network frames.
5. Physical/network hardware transmits it.
6. The receiving system reverses the process.

TCP/IP gives us a structured way to understand this communication.

---

# 3. TCP/IP Model Layers

## Layer 4 — Application Layer

The Application Layer is where network applications and protocols interact with network services.

### Common protocols

| Protocol | Purpose                         |
| -------- | ------------------------------- |
| HTTP     | Web communication               |
| HTTPS    | Encrypted web communication     |
| DNS      | Name resolution                 |
| FTP      | File transfer                   |
| SSH      | Secure remote administration    |
| SMTP     | Sending email                   |
| DHCP     | Automatic network configuration |
| SNMP     | Network management              |

### Example

When you visit:

```text
https://example.com
```

Your browser uses application-layer protocols such as:

```text
HTTPS
DNS
```

---

## VAPT Relevance

Application-layer testing includes areas such as:

* HTTP request/response analysis
* Authentication testing
* Authorization testing
* Input validation
* API testing
* Security header analysis
* DNS security testing
* SSH configuration review
* FTP security assessment

Tools commonly used include:

```text
Burp Suite
Nmap
curl
dig
nslookup
Wireshark
```

> **Important:** Application-layer testing must only be performed against systems you are authorized to test.

---

# Layer 3 — Transport Layer

The Transport Layer provides communication between applications/processes.

The two major protocols are:

```text
TCP
UDP
```

---

# 4. TCP — Transmission Control Protocol

TCP is:

* Connection-oriented
* Reliable
* Ordered
* Error-aware
* Flow-controlled

TCP is useful when reliable delivery is important.

### Examples

```text
HTTPS
HTTP
SSH
FTP
SMTP
```

---

## TCP Three-Way Handshake

Before normal TCP communication begins, a connection is established.

```text
Client                    Server
  │                         │
  │ -------- SYN ---------> │
  │ <------ SYN + ACK ----- │
  │ -------- ACK ---------> │
  │                         │
  │    Connection Ready     │
```

### Step 1 — SYN

Client requests a TCP connection.

### Step 2 — SYN-ACK

Server acknowledges the request and responds.

### Step 3 — ACK

Client acknowledges the server.

The TCP connection is now established.

---

# 5. UDP — User Datagram Protocol

UDP is:

* Connectionless
* Lightweight
* Faster in many situations
* No built-in delivery guarantee
* No TCP-style handshake

Examples include:

```text
DNS
DHCP
VoIP
Streaming
Some gaming traffic
```

### TCP vs UDP

| Feature     | TCP        | UDP          |
| ----------- | ---------- | ------------ |
| Connection  | Yes        | No           |
| Reliability | Built-in   | No guarantee |
| Ordering    | Yes        | No guarantee |
| Handshake   | Yes        | No           |
| Overhead    | Higher     | Lower        |
| Common use  | HTTPS, SSH | DNS, VoIP    |

### Important

"UDP is always faster than TCP" is an oversimplification.

Actual performance depends on the application, network conditions, implementation, and protocol behavior.

---

# 6. Ports

The Transport Layer uses **port numbers** to identify services/processes.

Example:

```text
192.168.1.10:443
```

Here:

```text
192.168.1.10 = IP address
443           = port
```

Think:

```text
IP Address = Building address
Port       = Specific door/service
```

### Common ports

| Port | Common Service |
| ---: | -------------- |
|   21 | FTP            |
|   22 | SSH            |
|   23 | Telnet         |
|   25 | SMTP           |
|   53 | DNS            |
|   80 | HTTP           |
|  110 | POP3           |
|  143 | IMAP           |
|  443 | HTTPS          |
|  445 | SMB            |
| 3306 | MySQL          |
| 3389 | RDP            |

> A port number alone does **not** prove what service is actually running. Service detection is required.

---

# Layer 2 — Internet Layer

The Internet Layer is responsible primarily for:

* Logical addressing
* Routing
* Moving packets between networks

The most important protocol is:

```text
IP
```

Other important protocols include:

```text
ICMP
```

and, in IPv4 networking:

```text
ARP
```

ARP is often discussed separately because it operates around the boundary between the Internet and Network Access layers.

---

# 7. IP Addressing

IP addresses identify network interfaces logically.

### IPv4 example

```text
192.168.1.10
```

An IPv4 address contains:

```text
32 bits
```

IPv4 is normally written as four decimal octets:

```text
192.168.1.10
```

---

# 8. Public vs Private IP Addresses

Private IPv4 ranges include:

```text
10.0.0.0/8

172.16.0.0/12

192.168.0.0/16
```

These are commonly used inside private networks.

Public IP addresses are generally routable across the public Internet, subject to routing and other controls.

---

# 9. Routing

Routers move IP packets between networks.

Example:

```text
Your PC
   │
   ▼
Router
   │
   ▼
ISP
   │
   ▼
Internet
   │
   ▼
Web Server
```

Your computer usually checks its routing table to determine where traffic should be sent.

On Linux:

```bash
ip route
```

Example concept:

```text
Destination → Gateway → Interface
```

---

# 10. ICMP

**ICMP — Internet Control Message Protocol**

ICMP is used for network control, diagnostics, and error reporting.

A common example is:

```bash
ping example.com
```

Ping commonly uses:

```text
ICMP Echo Request
ICMP Echo Reply
```

### VAPT relevance

ICMP can help with:

* Connectivity testing
* Host discovery
* Troubleshooting
* Understanding filtering/firewall behavior

> A failed ping does **not** necessarily mean a host is offline. ICMP may simply be blocked or filtered.

---

# Layer 1 — Network Access Layer

This layer deals with communication over the local network technology.

It includes concepts related to:

* Ethernet
* Wi-Fi
* MAC addresses
* Frames
* Network interface hardware
* Physical transmission

Examples:

```text
Ethernet
Wi-Fi
```

---

# 11. MAC Address

A MAC address is used for communication at the local network/link level.

Example:

```text
00:1A:2B:3C:4D:5E
```

IP addresses and MAC addresses serve different purposes.

```text
IP  → Logical addressing
MAC → Local/link-layer addressing
```

---

# 12. Frames

At the Network Access Layer, data is transmitted using **frames**.

A simplified Ethernet frame contains information such as:

```text
Destination MAC
Source MAC
EtherType
Payload
FCS
```

---

# 13. TCP/IP Data Units

Different layers use different names for the data being handled.

```text
Application
    ↓
Data

Transport
    ↓
Segment (TCP)
Datagram (UDP)

Internet
    ↓
Packet

Network Access
    ↓
Frame
```

### Easy memory

```text
TCP  → Segment
UDP  → Datagram
IP   → Packet
Ethernet → Frame
```

---

# 14. Encapsulation

When data travels from an application toward the network, each layer adds information needed by that layer.

Example:

```text
Application Data
       ↓
TCP Header + Data
       ↓
IP Header + TCP Segment
       ↓
Ethernet Header + IP Packet
       ↓
Network Transmission
```

This process is called:

# Encapsulation

---

# 15. Decapsulation

At the destination, the receiving system removes the headers layer by layer.

```text
Frame
  ↓
Packet
  ↓
TCP Segment
  ↓
Application Data
```

This is called:

# Decapsulation

---

# 16. Complete Website Request Example

Suppose you open:

```text
https://example.com
```

A simplified flow is:

```text
1. Browser needs example.com
          ↓
2. DNS resolves the hostname
          ↓
3. Browser connects to server IP
          ↓
4. TCP connection is established
          ↓
5. TLS is negotiated for HTTPS
          ↓
6. HTTP request is sent
          ↓
7. Server processes request
          ↓
8. HTTP response returns
          ↓
9. Browser displays the page
```

The exact sequence can vary because of DNS caching, connection reuse, HTTP version, TLS behavior, proxies, CDNs, and other factors.

---

# 17. TCP/IP vs OSI Model

The OSI model has:

```text
7 Layers
```

The commonly taught TCP/IP model has:

```text
4 Layers
```

### Mapping

| OSI          | TCP/IP         |
| ------------ | -------------- |
| Application  | Application    |
| Presentation | Application    |
| Session      | Application    |
| Transport    | Transport      |
| Network      | Internet       |
| Data Link    | Network Access |
| Physical     | Network Access |

### Important

The models are **conceptual models**, not exact implementations.

Do not assume that every real-world protocol fits perfectly into only one OSI layer.

---

# 18. OSI vs TCP/IP — Easy Understanding

Think of OSI as a more detailed teaching model:

```text
OSI
7 separate layers
```

TCP/IP is more practical and commonly used to describe Internet networking:

```text
TCP/IP
4 broad layers
```

### Simple analogy

OSI:

```text
More detailed blueprint
```

TCP/IP:

```text
Practical Internet communication model
```

Both are useful.

---

# 19. VAPT Perspective

Understanding TCP/IP is extremely important for penetration testing.

A pentester needs to understand where a finding belongs.

### Example

```text
Open TCP port 22
        ↓
Transport layer
        ↓
SSH service
        ↓
Application layer
```

Another example:

```text
IP address
   ↓
Internet layer
```

Another:

```text
MAC address
   ↓
Network Access layer
```

---

# 20. Nmap and TCP/IP

Nmap uses networking concepts heavily.

Example:

```bash
nmap -sV 192.168.1.10
```

Conceptually:

```text
Target IP
   ↓
Transport protocols / ports
   ↓
Service detection
   ↓
Version information
```

Nmap may use different discovery and scanning mechanisms depending on the options, target environment, privileges, and network conditions.

---

# 21. Wireshark and TCP/IP

Wireshark allows you to inspect network traffic.

You may see:

```text
Ethernet
    ↓
IP
    ↓
TCP
    ↓
TLS / HTTP / DNS / SSH ...
```

This is one of the best ways to connect theoretical networking concepts with real packets.

---

# 22. Useful Linux Commands

### Show IP addresses

```bash
ip addr
```

### Show routing table

```bash
ip route
```

### Test connectivity

```bash
ping -c 4 example.com
```

### Show listening sockets

```bash
ss -lntup
```

### DNS lookup

```bash
dig example.com
```

### Basic HTTP request

```bash
curl -I https://example.com
```

Use these commands primarily in your own lab or against systems you are authorized to assess.

---

# 23. Important Differences

## IP vs MAC

```text
IP  → Logical address
MAC → Link/local network address
```

## Port vs IP

```text
IP   → Identifies a network destination/interface
Port → Identifies a transport-layer endpoint/service
```

## TCP vs UDP

```text
TCP → Reliable, connection-oriented
UDP → Connectionless, lightweight
```

## Packet vs Frame

```text
Packet → Internet layer
Frame  → Network Access layer
```

---

# 24. Common Beginner Mistakes

### ❌ Mistake 1

"IP address identifies a specific application."

### ✅ Correct

IP identifies a network destination/interface; the **port** helps identify a transport-layer service/endpoint.

---

### ❌ Mistake 2

"Port 443 always means HTTPS."

### ✅ Correct

Port 443 is commonly associated with HTTPS, but port numbers do not guarantee the actual service.

---

### ❌ Mistake 3

"If ping fails, the server is down."

### ✅ Correct

ICMP may be blocked or filtered.

---

### ❌ Mistake 4

"TCP is always slower than UDP."

### ✅ Correct

TCP has more protocol overhead, but actual performance depends on the application and network conditions.

---

### ❌ Mistake 5

"TCP/IP and OSI are exactly the same."

### ✅ Correct

They are different conceptual models with overlapping concepts.

---

# 25. Must-Know Interview Questions

## Q1. What is TCP/IP?

A practical networking model used to describe communication across networks and the Internet.

## Q2. How many layers does the commonly taught TCP/IP model have?

Four.

```text
Application
Transport
Internet
Network Access
```

## Q3. What is TCP?

A connection-oriented transport protocol that provides reliable, ordered delivery.

## Q4. What is UDP?

A connectionless transport protocol with low overhead and no built-in guarantee of delivery or ordering.

## Q5. What is an IP address?

A logical network-layer address used to identify a network destination/interface.

## Q6. What is a port?

A transport-layer identifier used to distinguish network services/endpoints on a host.

## Q7. What is encapsulation?

Adding protocol information as data moves down the networking stack.

## Q8. What is decapsulation?

Removing protocol information as data moves up the stack at the destination.

## Q9. What happens during a TCP three-way handshake?

```text
SYN
SYN-ACK
ACK
```

## Q10. What is ICMP?

A protocol used for network diagnostics, control, and error reporting.

## Q11. What is the difference between a packet and a frame?

A packet is associated with the Internet layer; a frame is associated with the Network Access/link layer.

## Q12. Why is TCP/IP important for pentesters?

Because understanding IPs, ports, protocols, routing, and application communication is fundamental to reconnaissance, traffic analysis, service enumeration, and vulnerability assessment.

---

# 26. Quick Revision Cheat Sheet

```text
TCP/IP = Practical networking model

4 Layers:

Application
    ↓
Transport
    ↓
Internet
    ↓
Network Access
```

### Remember

```text
Application → HTTP, HTTPS, DNS, SSH, FTP
Transport   → TCP, UDP, Ports
Internet    → IP, ICMP, Routing
Network     → Ethernet, Wi-Fi, MAC, Frames
```

### Data Units

```text
Application → Data
TCP         → Segment
UDP         → Datagram
IP          → Packet
Ethernet    → Frame
```

### TCP Handshake

```text
SYN
 ↓
SYN-ACK
 ↓
ACK
```

---

# 27. Practical Learning Path

Do not only memorize this chapter.

Practice the concepts.

### Level 1 — Beginner

Learn:

* IP address
* MAC address
* Port
* TCP
* UDP
* DNS
* HTTP/HTTPS

Practice:

```bash
ip addr
ip route
ss -lntup
ping -c 4 example.com
```

---

### Level 2 — Intermediate

Learn:

* TCP handshake
* Routing
* ARP
* Subnets
* NAT
* DHCP
* DNS resolution
* HTTP request/response

Practice:

```bash
dig example.com
curl -I https://example.com
```

---

### Level 3 — Advanced

Learn:

* TCP flags
* SYN/ACK/RST/FIN
* Connection states
* MTU
* Fragmentation
* NAT behavior
* IPv4 vs IPv6
* TLS basics
* Packet capture
* Network troubleshooting

Use:

```text
Wireshark
tcpdump
Nmap
Scapy
```

---

# 28. Mini Lab — See TCP/IP in Real Life

Use a system you own or a local lab.

### Step 1 — Find your IP

```bash
ip addr
```

### Step 2 — Check routing

```bash
ip route
```

### Step 3 — Check listening services

```bash
ss -lntup
```

### Step 4 — Capture traffic

Open Wireshark and capture traffic on your active interface.

Then perform:

```bash
ping -c 4 <your-router-ip>
```

Observe:

```text
Ethernet
   ↓
IP
   ↓
ICMP
```

Then open a website and observe how additional protocols appear.

This connects the **TCP/IP theory → real packets → practical security analysis**.

---

# 29. Final Mental Model

Whenever you see network communication, ask:

```text
WHO?
 ↓
IP address

WHICH SERVICE?
 ↓
Port

HOW IS IT TRANSPORTED?
 ↓
TCP / UDP

HOW DOES IT REACH THE NETWORK?
 ↓
IP / Routing

HOW DOES IT TRAVEL LOCALLY?
 ↓
Ethernet / Wi-Fi / MAC

WHAT APPLICATION IS USING IT?
 ↓
HTTP / DNS / SSH / etc.
```

This mindset is far more valuable than memorizing layer definitions.

---

# 30. Key Takeaway

The TCP/IP model gives you a practical way to understand how Internet communication works.

For cybersecurity, the most important concepts to master are:

```text
IP
↓
Ports
↓
TCP / UDP
↓
Routing
↓
DNS
↓
HTTP / HTTPS
↓
Packets & Frames
↓
Traffic Analysis
```

Once these concepts become clear, tools such as:

```text
Nmap
Wireshark
Burp Suite
tcpdump
Scapy
```

become much easier to understand.

> **Goal:** Don't just memorize the TCP/IP layers. Be able to look at a connection and explain **what is communicating, where it is going, which protocol is carrying it, and how the data moves through the network.**
