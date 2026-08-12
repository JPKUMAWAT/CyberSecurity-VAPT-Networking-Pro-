
## 1. What Is the TCP/IP Model?

The **TCP/IP model** is a practical way to understand how network communication works.

It describes how application data is:

```text
Application
    ↓
Transport
    ↓
Internet
    ↓
Network Access
    ↓
Physical Network
```

The name comes from two important protocols:

* **TCP** — Transmission Control Protocol
* **IP** — Internet Protocol

The Internet protocol suite is commonly called **TCP/IP**, although it contains many protocols besides TCP and IP. The Internet architecture does not have an official OSI-style standardized reference model with exactly these layers, so the commonly taught **4-layer TCP/IP model is a useful teaching model**, not a strict equivalent to OSI. 

---

# 2. TCP/IP's 4 Layers

| Layer             | Main Responsibility           | Examples             |
| ----------------- | ----------------------------- | -------------------- |
| 4. Application    | Network applications/services | HTTP, DNS, SSH, FTP  |
| 3. Transport      | End-to-end communication      | TCP, UDP             |
| 2. Internet       | Logical addressing & routing  | IPv4, IPv6, ICMP     |
| 1. Network Access | Local network delivery        | Ethernet, Wi-Fi, ARP |

### Easy memory

```text
Application  → What service?
Transport    → Which application/process?
Internet     → Which host/network?
Network      → How to deliver locally?
```

---

# 3. Layer 4 — Application Layer

The Application layer is where network applications and application protocols operate.

### Examples

```text
HTTP    → Web
HTTPS   → Secure web communication
DNS     → Name resolution
SSH     → Remote administration
FTP     → File transfer
SMTP    → Email transfer
```

### Example

When you open:

```text
https://example.com
```

your browser uses application-layer protocols to communicate with the web server.

---

## VAPT Relevance

During an authorized assessment, the Application layer is extremely important because testers may examine:

* HTTP responses
* DNS behavior
* Authentication
* Application protocols
* Headers
* Cookies
* Service configuration

For example:

```text
Browser
   ↓
HTTPS
   ↓
TCP
   ↓
IP
   ↓
Ethernet/Wi-Fi
```

---

# 4. Layer 3 — Transport Layer

The Transport layer provides communication between applications/processes.

The two protocols you must know are:

```text
TCP
UDP
```

---

# 5. TCP — Transmission Control Protocol

TCP provides a **reliable, ordered byte-stream service** and uses port numbers to identify application services. ([RFC Editor][1])

### Important characteristics

* Connection-oriented
* Reliable delivery
* Ordered byte stream
* Uses acknowledgments
* Retransmits lost data
* Uses sequence numbers
* Uses port numbers

### Common TCP applications

```text
HTTP/HTTPS
SSH
FTP
SMTP
```

---

# 6. TCP Three-Way Handshake

Before normal TCP data exchange, the endpoints establish a connection.

```text
Client                         Server
  |                              |
  | -------- SYN --------------> |
  |                              |
  | <------ SYN + ACK ---------- |
  |                              |
  | -------- ACK --------------> |
  |                              |
  |       Connection Ready       |
```

### Easy explanation

### 1. SYN

Client:

> "I want to establish a TCP connection."

### 2. SYN-ACK

Server:

> "I received your request and I'm ready."

### 3. ACK

Client:

> "I received your response."

The TCP specification describes this as the **three-way handshake** used to establish the connection. ([RFC Editor][1])

---

# 7. TCP Flags You Should Know

| Flag | Meaning                         |
| ---- | ------------------------------- |
| SYN  | Synchronize sequence numbers    |
| ACK  | Acknowledgment                  |
| FIN  | Graceful connection termination |
| RST  | Reset/abort connection          |
| PSH  | Push data to application        |
| URG  | Urgent pointer significant      |

For beginner/interview purposes, focus especially on:

```text
SYN
ACK
FIN
RST
```

### VAPT relevance

When analyzing traffic in Wireshark or understanding Nmap behavior, TCP flags are very useful.

Example:

```text
SYN → SYN/ACK
```

can indicate that a TCP service is accepting connections.

---

# 8. UDP — User Datagram Protocol

UDP is a connectionless transport protocol.

Unlike TCP, UDP does not establish a TCP-style connection before sending data.

### Characteristics

* Connectionless
* No built-in retransmission
* No TCP-style ordering
* Low protocol overhead
* Uses ports
* Useful where applications prefer speed or simplicity

### Common examples

```text
DNS
DHCP
NTP
VoIP
Streaming applications
```

### Important misconception

❌ "UDP is always faster than TCP."

Better:

> UDP has less protocol overhead and does not provide TCP's reliability mechanisms, but actual application performance depends on the network and application.

---

# 9. TCP vs UDP

| Feature        | TCP                 | UDP                        |
| -------------- | ------------------- | -------------------------- |
| Connection     | Connection-oriented | Connectionless             |
| Reliability    | Yes                 | Not provided by UDP itself |
| Ordering       | Yes                 | Not provided               |
| Retransmission | Yes                 | Not provided               |
| Handshake      | Yes                 | No TCP handshake           |
| Overhead       | Higher              | Lower                      |
| Ports          | Yes                 | Yes                        |
| Example        | HTTPS, SSH          | DNS, DHCP                  |

### Remember

```text
TCP → Reliability
UDP → Simplicity / low overhead
```

---

# 10. Ports

An IP address identifies a host/interface.

A **port number** helps identify the application/service endpoint on that host.

Think:

```text
IP Address = Building
Port        = Door
Service     = Person/office behind the door
```

Example:

```text
192.168.1.10:22
```

means:

```text
Host = 192.168.1.10
Port = 22
```

Port 22 is conventionally associated with SSH.

---

# 11. Important Ports

|  Port | Common Service |
| ----: | -------------- |
| 20/21 | FTP            |
|    22 | SSH            |
|    23 | Telnet         |
|    25 | SMTP           |
|    53 | DNS            |
| 67/68 | DHCP           |
|    80 | HTTP           |
|   110 | POP3           |
|   123 | NTP            |
|   143 | IMAP           |
|   443 | HTTPS          |
|   445 | SMB            |
|  3306 | MySQL          |
|  3389 | RDP            |

### Important

A port number **does not guarantee** which service is actually running there.

For example:

```text
443 ≠ automatically HTTPS
```

A service can technically run on a non-standard port.

---

# 12. Layer 3 — Internet Layer

The Internet layer is responsible mainly for logical addressing and routing packets between networks.

Important protocols include:

```text
IPv4
IPv6
ICMP
```

---

# 13. IP Address

An IP address identifies a network interface using an Internet Protocol address.

Example IPv4:

```text
192.168.1.20
```

IPv4 uses **32 bits**.

```text
192       .168       .1        .20
 ↓          ↓         ↓          ↓
8 bits    8 bits    8 bits    8 bits

Total = 32 bits
```

IPv6 uses **128 bits**.

Example:

```text
2001:db8::10
```

---

# 14. IPv4 vs IPv6

| Feature       | IPv4         | IPv6                     |
| ------------- | ------------ | ------------------------ |
| Address size  | 32-bit       | 128-bit                  |
| Example       | 192.168.1.10 | 2001:db8::10             |
| Address space | Smaller      | Vast                     |
| Notation      | Decimal      | Hexadecimal              |
| Broadcast     | Supported    | No traditional broadcast |

### VAPT relevance

You should not assume:

```text
IPv4 only
```

Modern networks can use:

```text
IPv4
IPv6
Dual-stack
```

IPv6 can therefore introduce additional addressing and security considerations.

---

# 15. Routing

Routers forward packets between networks.

Example:

```text
Your PC
192.168.1.10
     |
     v
Router
192.168.1.1
     |
     v
Internet
     |
     v
Web Server
203.0.113.10
```

Your computer checks whether the destination is local.

If it isn't local, traffic normally goes toward a configured **default gateway**.

---

# 16. Default Gateway

A default gateway is typically the router/interface used to reach destinations outside the local network when no more specific route is available.

Example:

```text
PC
192.168.1.20

Gateway
192.168.1.1
```

Traffic to:

```text
192.168.1.50
```

may remain inside the local network.

Traffic to:

```text
8.8.8.8
```

will normally be forwarded toward the gateway.

---

# 17. ICMP

**ICMP = Internet Control Message Protocol**

It is used for network control, diagnostics, and error reporting.

A familiar example is:

```bash
ping 8.8.8.8
```

`ping` commonly uses ICMP Echo Request and Echo Reply.

### Important misconception

❌:

> Ping failed = host is definitely offline.

Correct:

> Ping failure only tells you that the expected ICMP exchange did not succeed. A host or firewall may block ICMP while other services remain reachable.

---

# 18. Layer 1 — Network Access Layer

This layer deals with local network delivery.

Examples:

```text
Ethernet
Wi-Fi
```

It handles technologies used to move data across the local link.

---

# 19. MAC Address

A MAC address is a link-layer address used by network technologies such as Ethernet.

Example:

```text
00:1A:2B:3C:4D:5E
```

### IP vs MAC

| IP                                | MAC                                     |
| --------------------------------- | --------------------------------------- |
| Logical/network address           | Link-layer address                      |
| Used for routing between networks | Used for local-link delivery            |
| IPv4/IPv6                         | Ethernet/Wi-Fi addressing               |
| Can change                        | Can also be changed/spoofed in software |

### Easy memory

```text
IP  → Where?
MAC → Local delivery to which interface?
Port → Which application?
```

---

# 20. ARP — Important IPv4 Concept

Inside an IPv4 Ethernet network, a host may need to discover the MAC address associated with a local IPv4 address.

Example:

```text
PC wants:
192.168.1.1

        ARP
         ↓
"What is the MAC for 192.168.1.1?"

         ↓

Router replies with MAC

         ↓

Ethernet frame can be sent
```

ARP is associated with local-link IPv4 operation, not with routing across the entire Internet.

---

# 21. Encapsulation

This is one of the **most important concepts**.

When an application sends data, each lower layer adds information needed for communication.

```text
Application Data
       ↓
TCP Header + Data
       ↓
IP Header + TCP Segment
       ↓
Ethernet Header + IP Packet
       ↓
Bits/signals on the medium
```

A simplified view:

```text
Application
   DATA
    ↓
Transport
   [TCP][DATA]
    ↓
Internet
   [IP][TCP][DATA]
    ↓
Network Access
[ETH][IP][TCP][DATA][FCS]
```

---

# 22. PDU Names

| Layer          | Common PDU term                |
| -------------- | ------------------------------ |
| Application    | Data                           |
| Transport      | Segment (TCP) / Datagram (UDP) |
| Internet       | IP Packet / Datagram           |
| Network Access | Frame                          |

### Easy memory

```text
Data
 ↓
Segment
 ↓
Packet
 ↓
Frame
```

---

# 23. Decapsulation

At the destination, the reverse process happens.

```text
Frame
  ↓
Packet
  ↓
TCP Segment
  ↓
Application Data
```

Example:

```text
Web Server
    ↓
Ethernet frame received
    ↓
IP information processed
    ↓
TCP information processed
    ↓
HTTP/application data delivered
```

---

# 24. Complete Website Request Flow

Suppose you visit:

```text
https://example.com
```

A simplified flow is:

```text
Browser
  |
  | DNS
  v
DNS Resolver
  |
  | IP address
  v
Server IP
  |
  | TCP connection
  v
TCP 3-way handshake
  |
  | TLS
  v
Encrypted HTTPS communication
  |
  | HTTP request
  v
Web Server
  |
  | HTTP response
  v
Browser
```

This is a simplified conceptual flow; real systems can involve caches, proxies, CDNs, load balancers, and other components.

---

# 25. TCP/IP vs OSI

The models are useful for different purposes.

| TCP/IP         | Approximate OSI mapping              |
| -------------- | ------------------------------------ |
| Application    | Application + Presentation + Session |
| Transport      | Transport                            |
| Internet       | Network                              |
| Network Access | Data Link + Physical                 |

### Important

Do **not** say:

> "TCP/IP has exactly the same layers as OSI."

Better:

> "The commonly taught TCP/IP 4-layer model maps approximately to OSI's 7 layers."

---

# 26. VAPT Perspective

Understanding TCP/IP is essential before learning advanced VAPT.

A basic assessment workflow might look conceptually like:

```text
Target
  ↓
Network discovery
  ↓
IP / hostname identification
  ↓
Port discovery
  ↓
Service identification
  ↓
Application enumeration
  ↓
Vulnerability assessment
```

Each stage depends on networking knowledge.

---

# 27. Example: Understanding an Open Port

Suppose an authorized lab machine shows:

```text
192.168.56.10:22
```

You can reason:

```text
192.168.56.10
       ↓
IP address
       ↓
Port 22
       ↓
TCP endpoint
       ↓
Likely SSH service
```

But you should **verify the actual service** instead of assuming it solely from the port number.

---

# 28. Safe Lab Commands

Only use these against systems you own or are explicitly authorized to test.

### Linux

```bash
ip addr
```

View interfaces and addresses.

```bash
ip route
```

View routing table.

```bash
ss -tuln
```

View listening TCP/UDP sockets.

```bash
ping -c 4 127.0.0.1
```

Test local connectivity.

### Windows

```powershell
ipconfig
```

View network configuration.

```powershell
route print
```

View routing table.

```powershell
netstat -ano
```

View network connections and listening ports.

---

# 29. Wireshark Mental Model

When looking at a packet capture, think from outside → inside:

```text
Ethernet
   ↓
IP
   ↓
TCP/UDP
   ↓
Application Protocol
```

For example:

```text
Ethernet
  └── IPv4
       └── TCP
            └── TLS
                 └── Application data
```

This layered thinking makes Wireshark much easier to understand.

---

# 30. Common Beginner Mistakes

### ❌ Mistake 1

"IP address identifies a specific application."

### ✅ Correct

IP identifies a host/interface; **port numbers identify transport endpoints/services**.

---

### ❌ Mistake 2

"Port 443 means HTTPS."

### ✅ Correct

443 is the conventional port associated with HTTPS, but the actual protocol should be verified.

---

### ❌ Mistake 3

"Ping failed, therefore the machine is down."

### ✅ Correct

ICMP may be filtered while TCP/UDP services remain reachable.

---

### ❌ Mistake 4

"UDP has no ports."

### ✅ Correct

UDP uses source and destination port numbers.

---

### ❌ Mistake 5

"TCP guarantees the application will receive the data."

### ✅ Correct

TCP provides reliable, ordered delivery of a byte stream between TCP endpoints; application-level success is a separate matter. ([RFC Editor][1])

---

# 31. Interview Questions

### Q1. What is TCP/IP?

A practical protocol-suite model used to understand Internet/network communication.

### Q2. How many layers are commonly taught in the TCP/IP model?

**4 layers.**

### Q3. What does TCP provide?

Reliable, ordered byte-stream communication between endpoints. ([RFC Editor][1])

### Q4. What is UDP?

A connectionless transport protocol with lower protocol overhead and without TCP's built-in reliability mechanisms.

### Q5. What is a port?

A transport-layer identifier used to distinguish application/service endpoints.

### Q6. What is an IP address?

A logical Internet Protocol address used to identify a network interface/address for communication.

### Q7. What is a MAC address?

A link-layer address used for local network delivery technologies such as Ethernet.

### Q8. What is encapsulation?

Adding protocol-specific information as data moves down the networking stack.

### Q9. What is decapsulation?

Removing/processing those headers as data moves up the stack at the destination.

### Q10. Why is TCP's handshake called three-way?

Because the normal connection establishment uses SYN, SYN-ACK, and ACK exchanges. ([RFC Editor][1])

---

# 32. Scenario-Based Questions

### Scenario 1

You can access a website, but `ping` fails.

**Does that prove the server is offline?**

> No. ICMP may be blocked.

---

### Scenario 2

You see:

```text
10.0.0.5:22
```

What does `22` represent?

> TCP/UDP port number; in this context it is commonly associated with SSH, but the service should be verified.

---

### Scenario 3

A packet is going from your laptop to another network.

Which device normally forwards it toward the remote network?

> A router/default gateway, according to the routing table.

---

### Scenario 4

A browser communicates with a web server over TCP.

Which layer handles the TCP connection?

> Transport layer.

---

### Scenario 5

A packet capture shows:

```text
Ethernet → IPv4 → TCP → TLS
```

Which layer does TCP belong to?

> Transport layer.

---

# 33. MCQs

### 1. Which layer uses TCP?

A. Application
B. Transport
C. Internet
D. Network Access

**Answer: B**

### 2. IPv4 address size is:

A. 16-bit
B. 32-bit
C. 64-bit
D. 128-bit

**Answer: B**

### 3. IPv6 address size is:

A. 32-bit
B. 64-bit
C. 128-bit
D. 256-bit

**Answer: C**

### 4. Which protocol provides a reliable byte stream?

A. UDP
B. TCP
C. ARP
D. ICMP

**Answer: B**

### 5. What does a port identify?

A. Physical cable
B. Application/service endpoint
C. MAC manufacturer
D. Router

**Answer: B**

### 6. Which protocol is commonly associated with DNS?

A. TCP only
B. UDP only
C. Both TCP and UDP depending on operation
D. ICMP

**Answer: C**

### 7. What is the first packet of a normal TCP handshake?

A. ACK
B. FIN
C. SYN
D. RST

**Answer: C**

### 8. What does RST generally indicate?

A. Reset/abort
B. Start connection
C. DNS lookup
D. Encryption

**Answer: A**

### 9. Which device primarily forwards traffic between IP networks?

A. Switch
B. Router
C. Hub
D. NIC

**Answer: B**

### 10. Which is a link-layer technology?

A. Ethernet
B. TCP
C. HTTP
D. DNS

**Answer: A**

### 11. What is encapsulation?

A. Encrypting everything
B. Adding protocol information while moving down the stack
C. Removing packets
D. Assigning an IP

**Answer: B**

### 12. Which command displays the Linux routing table?

A. `pwd`
B. `ip route`
C. `whoami`
D. `ls`

**Answer: B**

### 13. Which Windows command displays IP configuration?

A. `ipconfig`
B. `chmod`
C. `ifconfig`
D. `route`

**Answer: A**

### 14. Which is a TCP protocol example?

A. SSH
B. ARP
C. Ethernet
D. ICMP

**Answer: A**

### 15. Does port 443 mathematically guarantee HTTPS?

A. Yes
B. No

**Answer: B**

---

# 34. Must Remember 🧠

```text
TCP/IP = practical Internet protocol suite

4 Commonly Taught Layers:

Application
    ↓
Transport
    ↓
Internet
    ↓
Network Access
```

```text
TCP  → Reliable + ordered byte stream
UDP  → Connectionless + low overhead
```

```text
IP    → Logical addressing/routing
MAC   → Local-link addressing
Port  → Application/service endpoint
```

```text
TCP Handshake:

SYN
 ↓
SYN-ACK
 ↓
ACK
```

```text
Encapsulation:

Data
 ↓
Segment
 ↓
Packet
 ↓
Frame
```

---

# 35. Practical Lab Ideas 🧪

### Lab 1 — See Your Network

Linux:

```bash
ip addr
ip route
ss -tuln
```

Windows:

```powershell
ipconfig
route print
netstat -ano
```

Identify:

* Your IP
* Default gateway
* Interfaces
* Listening ports
* Routing entries

---

### Lab 2 — TCP Traffic

Use a machine/service you control and capture the connection in Wireshark.

Try to identify:

```text
SYN
SYN-ACK
ACK
Data
FIN
```

---

### Lab 3 — Complete Mental Flow

Take one website request and explain it yourself:

```text
DNS
 ↓
IP
 ↓
Routing
 ↓
TCP
 ↓
TLS/HTTP
 ↓
Response
```

If you can explain this without memorizing it, your TCP/IP foundation is becoming strong.

---

# 36. Final Mental Model

Whenever you see network traffic, ask these questions:

```text
1. WHAT application is communicating?
          ↓
2. WHICH transport protocol?
          ↓
3. WHICH ports?
          ↓
4. WHICH IP addresses?
          ↓
5. IS the destination local or remote?
          ↓
6. WHICH router/gateway is used?
          ↓
7. HOW is it delivered on the local network?
          ↓
8. WHAT does the packet capture show?
```

### The complete picture

```text
             APPLICATION
       HTTP / DNS / SSH / etc.
                ↓
             TRANSPORT
            TCP / UDP
                ↓
              IP
         IPv4 / IPv6
                ↓
        NETWORK ACCESS
       Ethernet / Wi-Fi
                ↓
          Physical medium
```

This mental model is the foundation for understanding **Nmap, Wireshark, DNS, HTTP, firewalls, IDS/IPS, VPNs, network enumeration, and later VAPT work**.

---

## Key Takeaways

* TCP/IP is best understood as a **protocol suite plus a commonly taught layered model**.
* TCP provides reliable, ordered byte-stream communication.
* UDP does not provide TCP's built-in reliability mechanisms.
* IP handles logical addressing and routing.
* Ports distinguish transport endpoints/services.
* MAC addresses are used for local-link delivery.
* TCP normally starts with a three-way handshake.
* Encapsulation happens while data moves down the stack.
* Decapsulation happens at the destination.
* **Networking understanding > memorizing commands**.
