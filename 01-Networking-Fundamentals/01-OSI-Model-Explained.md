

# OSI Model Explained

## 1. What is the OSI Model?

OSI stands for **Open Systems Interconnection**.

It is a 7-layer conceptual model used to understand how data moves between two devices over a network.

The OSI model does NOT mean that every real-world network literally operates using seven separate layers. It is mainly a learning, troubleshooting, and communication model.

### Why is OSI important for cybersecurity?

A pentester needs to understand where a technology operates.

For example:

- MAC address → Layer 2
- IP address → Layer 3
- TCP/UDP → Layer 4
- HTTP/SSH/DNS → Layer 7

Understanding the layer helps you understand the attack surface.

---

# 2. The 7 OSI Layers

Remember:

> **Please Do Not Throw Sausage Pizza Away**

| Layer | Name | Main Responsibility | Examples |
|---|---|---|---|
| 7 | Application | Network services used by applications | HTTP, DNS, SSH |
| 6 | Presentation | Data representation/translation | Encoding, encryption concepts |
| 5 | Session | Session establishment/management | Sessions, RPC concepts |
| 4 | Transport | End-to-end delivery | TCP, UDP |
| 3 | Network | Logical addressing/routing | IPv4, IPv6, routers |
| 2 | Data Link | Frames, MAC addressing | Ethernet, switches |
| 1 | Physical | Electrical/radio/physical signals | Cable, fiber, Wi-Fi radio |

---

# 3. Layer 1 — Physical

## Definition

The Physical layer deals with the transmission of raw bits over a physical medium.

Examples:

- Ethernet cable
- Fiber
- Radio signals
- Wi-Fi physical transmission

Data unit:

> Bits

### Example

A computer sends electrical/light/radio signals representing:

```text
101101001010...

### VAPT relevance

Physical security is different from normal network pentesting.

Examples:

* Unauthorized physical access
* Rogue devices
* Network cable exposure

---

# 4. Layer 2 — Data Link

Layer 2 handles communication within the local network.

Important concepts:

* MAC address
* Ethernet frames
* Switches
* ARP-related local network operation

Data unit:

> Frame

Example:

```text
Source MAC → Destination MAC
```

### Device

Switches primarily operate at Layer 2.

### Security relevance

Important concepts:

* MAC address
* ARP
* VLANs
* Switch configuration
* Network segmentation

---

# 5. Layer 3 — Network

Layer 3 handles logical addressing and routing between networks.

Important protocols:

* IPv4
* IPv6
* ICMP

Data unit:

> Packet

Example:

```text
192.168.1.10 → 8.8.8.8
```

Routers operate primarily at Layer 3.

### VAPT relevance

This layer is extremely important for:

* Network discovery
* Routing analysis
* Network segmentation
* IP exposure
* Firewall rules
* Attack-surface mapping

Useful Kali commands:

```bash
ip addr
ip route
ping 192.168.1.1
```

Use only on systems/networks you are authorized to test.

---

# 6. Layer 4 — Transport

Layer 4 provides end-to-end transport between applications.

Main protocols:

## TCP

Connection-oriented and reliable.

Important concepts:

```text
SYN
SYN-ACK
ACK
```

## UDP

Connectionless and does not provide TCP-style delivery guarantees.

Examples:

* DNS commonly uses UDP
* Some streaming/real-time applications use UDP

Data units:

TCP → Segment

UDP → Datagram

### VAPT relevance

Port scanning heavily depends on understanding Layer 4.

Example:

```text
192.168.1.10:22
```

Means:

```text
IP address = 192.168.1.10
Port = 22
```

Port 22 commonly hosts SSH.

---

# 7. Layer 5 — Session

The Session layer deals conceptually with establishing, maintaining, and terminating communication sessions.

In modern TCP/IP networking, these responsibilities are often implemented across multiple layers rather than as a distinct standalone layer.

### Important

Do NOT memorize:

> "Every network has a separate Session-layer protocol."

That would be misleading.

Think of Layer 5 as a conceptual way to understand session management.

---

# 8. Layer 6 — Presentation

The Presentation layer concerns how data is represented.

Examples of concepts:

* Encoding
* Serialization
* Data formatting
* Encryption/decryption concepts

Examples:

```text
UTF-8
JSON
XML
```

Again, modern systems frequently implement these functions inside application libraries/protocols rather than as a separate OSI layer.

---

# 9. Layer 7 — Application

This is where network services used by applications are commonly discussed.

Examples:

* HTTP
* HTTPS
* DNS
* SSH
* FTP
* SMTP

Example:

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
```

### VAPT relevance

This is one of the most important layers for web/application pentesting.

Examples:

* HTTP request analysis
* Authentication testing
* API testing
* Security headers
* Input validation
* Session management

---

# 10. Complete Example — Opening a Website

Suppose you visit:

```text
https://example.com
```

Conceptually:

```text
Layer 7 → HTTPS / HTTP
Layer 6 → Data representation/encryption functions
Layer 5 → Session-related behavior
Layer 4 → TCP
Layer 3 → IP
Layer 2 → Ethernet/Wi-Fi
Layer 1 → Physical transmission
```

The actual implementation is more complex than this simplified diagram.

---

# 11. OSI Troubleshooting Method

When troubleshooting, move layer by layer.

```text
Physical
   ↓
Data Link
   ↓
Network
   ↓
Transport
   ↓
Application
```

Example:

If a website is unreachable:

### Layer 1

Is the network connection available?

### Layer 2

Is the interface connected?

```bash
ip link
```

### Layer 3

Does the machine have an IP?

```bash
ip addr
```

Can it reach the gateway?

```bash
ping <gateway>
```

### Layer 3 routing

```bash
ip route
```

### Layer 4

Is the service reachable on its port?

### Layer 7

Does the application actually respond?

---

# 12. VAPT Mental Model

Think:

```text
Physical
   ↓
MAC / VLAN
   ↓
IP / Routing
   ↓
TCP / UDP / Ports
   ↓
Services
   ↓
Applications
   ↓
Authentication / Data
```

This helps you move from:

> "There is an IP"

to:

> "What service is running?"

to:

> "What application is behind it?"

to:

> "What security controls are present?"

---

# 13. Interview Questions

### Q1. What is OSI?

A conceptual seven-layer model for understanding network communication.

### Q2. Which layer uses IP?

Layer 3.

### Q3. Which layer uses TCP/UDP?

Layer 4.

### Q4. Which layer contains HTTP?

Layer 7 in the OSI mapping commonly used for learning.

### Q5. What is the Layer 2 data unit?

Frame.

### Q6. What is the Layer 3 data unit?

Packet.

### Q7. What is the Layer 4 data unit?

TCP segment / UDP datagram.

### Q8. Which device primarily operates at Layer 2?

Switch.

### Q9. Which device primarily operates at Layer 3?

Router.

---

# 14. MCQs

### 1. IP operates primarily at which OSI layer?

A. Layer 2
B. Layer 3
C. Layer 4
D. Layer 7

**Answer: B**

### 2. TCP belongs to:

A. Layer 2
B. Layer 3
C. Layer 4
D. Layer 6

**Answer: C**

### 3. Ethernet frames are associated with:

A. Layer 1
B. Layer 2
C. Layer 4
D. Layer 7

**Answer: B**

### 4. HTTP is generally mapped to:

A. Layer 1
B. Layer 2
C. Layer 4
D. Layer 7

**Answer: D**

### 5. A router primarily works at:

A. Layer 1
B. Layer 2
C. Layer 3
D. Layer 7

**Answer: C**

---

# 15. Must Remember

```text
L1 → Physical → Bits
L2 → Data Link → Frames / MAC
L3 → Network → Packets / IP
L4 → Transport → TCP / UDP / Ports
L5 → Session
L6 → Presentation
L7 → Application → HTTP / DNS / SSH
```

## Final Mental Model

> MAC helps local delivery.
> IP helps routing between networks.
> TCP/UDP provides transport.
> Ports identify service endpoints.
> Application protocols define what the service does.

````

---

# 02 — TCP/IP Model Explained

```markdown
# TCP/IP Model Explained

## 1. What is TCP/IP?

TCP/IP is the practical networking protocol suite used by the Internet and most modern networks.

Unlike OSI, TCP/IP is not primarily a seven-layer conceptual teaching model.

A common four-layer TCP/IP model is:

| TCP/IP Layer | Examples |
|---|---|
| Application | HTTP, DNS, SSH, DHCP |
| Transport | TCP, UDP |
| Internet | IPv4, IPv6, ICMP |
| Network Access / Link | Ethernet, Wi-Fi |

---

# 2. OSI vs TCP/IP

| OSI | TCP/IP |
|---|---|
| 7 layers | Commonly 4 layers |
| Conceptual/reference model | Practical protocol suite/model |
| Useful for learning | Used by real networks |

Mapping:

```text
OSI 7 Application
OSI 6 Presentation       → TCP/IP Application
OSI 5 Session

OSI 4 Transport          → TCP/IP Transport

OSI 3 Network            → TCP/IP Internet

OSI 2 Data Link
OSI 1 Physical           → TCP/IP Link/Network Access
````

---

# 3. Application Layer

Examples:

```text
HTTP
HTTPS
DNS
SSH
FTP
SMTP
DHCP
```

This layer provides network services to applications.

### VAPT relevance

A pentester often begins application/service enumeration here.

Questions:

```text
What service?
What protocol?
What version?
What authentication?
What data?
What security controls?
```

---

# 4. Transport Layer

Main protocols:

## TCP

Reliable, connection-oriented transport.

Important:

```text
SYN
SYN-ACK
ACK
```

## UDP

Connectionless transport.

Important for understanding:

* DNS
* VoIP
* Streaming
* Some discovery protocols

### VAPT

Port numbers are extremely important.

```text
IP:Port
```

Example:

```text
192.168.1.10:443
```

---

# 5. Internet Layer

Main protocols:

* IPv4
* IPv6
* ICMP

Main responsibility:

> Logical addressing and routing.

Example:

```text
192.168.1.10
      ↓
Router
      ↓
10.0.0.10
```

---

# 6. Link Layer

Responsible for communication over the local network medium.

Examples:

* Ethernet
* Wi-Fi
* MAC addressing

---

# 7. Real Traffic Flow

Suppose:

```text
Client → Web Server
```

Conceptually:

```text
HTTP request
    ↓
TCP segment
    ↓
IP packet
    ↓
Ethernet/Wi-Fi frame
    ↓
Network medium
```

At the destination, the process is reversed.

---

# 8. Encapsulation

Sender:

```text
Application Data
      ↓
TCP/UDP Header + Data
      ↓
IP Header + Segment
      ↓
Link Header + Packet
```

Receiver:

```text
Frame
 ↓
Packet
 ↓
Segment/Datagram
 ↓
Application Data
```

This is called:

> Encapsulation and decapsulation.

---

# 9. VAPT Perspective

TCP/IP knowledge helps you understand:

* Network boundaries
* Ports
* Services
* Routing
* Firewalls
* Network segmentation
* Traffic analysis
* Enumeration

A strong pentester doesn't just run a scanner.

They understand what the scanner is actually observing.

---

# 10. Practical Kali Commands

Authorized lab only:

```bash
ip addr
```

View interfaces and IP addresses.

```bash
ip route
```

View routing table.

```bash
ss -tuln
```

View listening TCP/UDP sockets.

```bash
ping <authorized-host>
```

Test basic IP connectivity.

---

# 11. Interview Questions

### What is TCP/IP?

A practical networking protocol suite used by modern networks and the Internet.

### TCP vs UDP?

TCP provides connection-oriented reliable delivery; UDP is connectionless and does not provide TCP's reliability mechanisms.

### What does IP do?

Provides logical addressing and routing.

### What does a port represent?

A transport-layer endpoint used to identify a service/application endpoint on a host.

### What is encapsulation?

Adding protocol-specific information as data moves down the networking stack.

---

# Must Remember

```text
Application → HTTP/DNS/SSH
Transport   → TCP/UDP
Internet    → IP/ICMP
Link        → Ethernet/Wi-Fi
```

Mental model:

> Application = What am I communicating?
>
> Transport = How should it be delivered?
>
> Internet = Where should it go?
>
> Link = How do I send it on this local network?

````

---

# 03 — Data Encapsulation Process

```markdown
# Data Encapsulation Process

## 1. What is Encapsulation?

Encapsulation is the process of adding protocol-specific information as application data moves down the networking stack.

Example:

```text
Application
    ↓
TCP
    ↓
IP
    ↓
Ethernet
````

Each layer adds information needed for communication.

---

# 2. Simple Example

Suppose you send:

```text
Hello
```

The application starts with:

```text
DATA
```

TCP adds a header:

```text
TCP HEADER + DATA
```

IP adds another header:

```text
IP HEADER + TCP HEADER + DATA
```

Ethernet adds a frame header/trailer:

```text
ETHERNET HEADER
+
IP HEADER
+
TCP HEADER
+
DATA
+
ETHERNET TRAILER
```

The exact wire representation varies by protocol and technology, but this is the useful conceptual model.

---

# 3. Data Names at Different Layers

| Layer       | Common Data Unit               |
| ----------- | ------------------------------ |
| Application | Data / Message                 |
| Transport   | Segment (TCP) / Datagram (UDP) |
| Internet    | Packet                         |
| Link        | Frame                          |
| Physical    | Bits/signals                   |

---

# 4. Encapsulation Flow

```text
Application Data
       ↓
[TCP Header + Data]
       ↓
[IP Header + TCP Header + Data]
       ↓
[Ethernet Header + IP Packet + Trailer]
       ↓
Bits/signals
```

---

# 5. Decapsulation

At the receiving machine, headers are processed in the reverse direction.

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

> Decapsulation.

---

# 6. Why Headers Matter

Headers contain control information.

Examples:

### TCP

May contain:

* Source port
* Destination port
* Sequence number
* Acknowledgment number
* Flags
* Window information

### IP

Contains information such as:

* Source IP
* Destination IP
* TTL/Hop Limit
* Protocol/Next Header information

### Ethernet

Contains:

* Source MAC
* Destination MAC
* EtherType

---

# 7. Example: HTTPS Request

Simplified:

```text
Application
HTTPS data
   ↓
TCP
Source port → Destination port 443
   ↓
IP
Source IP → Destination IP
   ↓
Ethernet/Wi-Fi
Source MAC → Destination MAC
```

Notice:

> MAC addresses are used for local-link delivery, while IP addresses identify endpoints at the network layer.

---

# 8. Router Behavior

A router receives a frame and processes the packet inside it.

Conceptually:

```text
Incoming Frame
      ↓
Remove/process link-layer information
      ↓
Inspect IP packet
      ↓
Routing decision
      ↓
Create outgoing link-layer frame
      ↓
Next network
```

The Layer-2 frame is normally rebuilt for the next link.

The IP packet continues toward its destination, although some IP fields such as TTL/Hop Limit are updated during forwarding.

---

# 9. VAPT Relevance

Encapsulation knowledge helps when:

* Reading Wireshark captures
* Understanding Nmap results
* Troubleshooting scans
* Understanding firewall behavior
* Analyzing TCP connections
* Investigating suspicious traffic
* Understanding network segmentation

---

# 10. Wireshark Mental Model

When looking at a packet, think:

```text
Frame
 └── Ethernet
      └── IP
           └── TCP/UDP
                └── Application protocol
```

Example:

```text
Ethernet
  ↓
IPv4
  ↓
TCP
  ↓
HTTP
```

---

# 11. Interview Questions

### What is encapsulation?

Adding protocol information as data moves down the networking stack.

### What is decapsulation?

Processing/removing protocol information as data moves up the receiving stack.

### What is a TCP segment?

The transport-layer data unit associated with TCP.

### What is an IP packet?

An IP-layer packet containing transport-layer information and application data.

### What is an Ethernet frame?

A link-layer data unit used to transport a network-layer packet over an Ethernet network.

---

# Must Remember

```text
Application → Data
TCP → Segment
IP → Packet
Ethernet → Frame
Physical → Bits
```

The golden flow:

```text
DATA
 ↓
SEGMENT
 ↓
PACKET
 ↓
FRAME
 ↓
BITS
```

Receiver:

```text
BITS
 ↓
FRAME
 ↓
PACKET
 ↓
SEGMENT
 ↓
DATA
```

````

---

# 04 — Common Ports & Services Cheatsheet

```markdown
# Common Ports & Services Cheatsheet

## 1. What is a Port?

A port is a transport-layer endpoint identified by a number.

Port numbers range from:

```text
0 – 65535
````

Ports help the operating system deliver network traffic to the appropriate application/service.

Example:

```text
192.168.1.10:22
```

Means:

```text
IP   = 192.168.1.10
Port = 22
```

---

# 2. Important Ports

|    Port | Protocol/Service     | Common Use                                    |
| ------: | -------------------- | --------------------------------------------- |
|      20 | FTP                  | FTP data                                      |
|      21 | FTP                  | FTP control                                   |
|      22 | SSH                  | Secure remote administration                  |
|      23 | Telnet               | Remote terminal; insecure by modern standards |
|      25 | SMTP                 | Mail transfer                                 |
|      53 | DNS                  | Name resolution                               |
|   67/68 | DHCP                 | Address configuration                         |
|      80 | HTTP                 | Web                                           |
|     110 | POP3                 | Email retrieval                               |
|     123 | NTP                  | Time synchronization                          |
|     135 | MS RPC               | Windows RPC                                   |
| 137-139 | NetBIOS              | Legacy Windows networking                     |
|     143 | IMAP                 | Email retrieval                               |
|     161 | SNMP                 | Network management                            |
|     389 | LDAP                 | Directory services                            |
|     443 | HTTPS                | Encrypted web                                 |
|     445 | SMB                  | Windows file/network sharing                  |
|     587 | SMTP submission      | Email submission                              |
|     636 | LDAPS                | LDAP over TLS                                 |
|     993 | IMAPS                | IMAP over TLS                                 |
|     995 | POP3S                | POP3 over TLS                                 |
|    1433 | Microsoft SQL Server | Database                                      |
|    3306 | MySQL                | Database                                      |
|    3389 | RDP                  | Windows remote desktop                        |
|    5432 | PostgreSQL           | Database                                      |
|    5900 | VNC                  | Remote desktop                                |

---

# 3. Important Warning

A port number does NOT guarantee the service.

For example:

```text
Port 80 → commonly HTTP
```

But an administrator can configure another service on port 80.

Therefore:

> Port number = clue, not proof.

This is why service detection matters.

---

# 4. TCP vs UDP

Some services primarily use TCP, some UDP, and some can use both depending on the protocol/version/configuration.

Examples:

```text
HTTP  → TCP commonly
HTTPS → TCP commonly
SSH   → TCP
DNS   → UDP commonly, TCP also used
DHCP  → UDP
NTP   → UDP
```

Do not blindly assume every protocol has only one transport protocol.

---

# 5. VAPT Workflow

When you discover:

```text
22/tcp open
```

Don't immediately conclude:

> "SSH is definitely vulnerable."

Instead:

```text
Open port
   ↓
Identify service
   ↓
Identify version/configuration
   ↓
Understand exposure
   ↓
Check security posture
   ↓
Validate findings safely
```

---

# 6. Safe Lab Commands

On your own machine/lab:

```bash
ss -tuln
```

Shows listening TCP/UDP sockets.

Authorized Nmap lab:

```bash
nmap -sV <authorized-target>
```

`-sV` requests service/version detection.

Example for a deliberately authorized practice target:

```bash
nmap -sV scanme.nmap.org
```

Always follow the target owner's testing rules.

---

# 7. Port States in Nmap

Common states include:

### open

An application is actively accepting connections.

### closed

The port is reachable, but no application is listening.

### filtered

A firewall/filter prevents Nmap from determining whether it is open.

Important:

> "Filtered" does NOT mean "secure."

It means Nmap could not determine the state normally because filtering interfered.

---

# 8. VAPT Thinking

Suppose you discover:

```text
22/tcp open
80/tcp open
443/tcp open
3306/tcp open
```

Your next questions should be:

```text
What services?
What versions?
Are they intended to be exposed?
Is the database supposed to be externally reachable?
Is encryption correctly configured?
Is authentication required?
Are there unnecessary services?
Are firewall rules/segmentation correct?
```

This is better than simply collecting a list of ports.

---

# 9. Common Beginner Mistakes

### Mistake 1

Thinking:

> Port 443 = automatically secure.

Wrong.

HTTPS can still have application vulnerabilities.

### Mistake 2

Thinking:

> Closed port = vulnerable.

Wrong.

Closed means no service is accepting connections there.

### Mistake 3

Thinking:

> Port number identifies the service with certainty.

Wrong.

Port numbers are conventions.

### Mistake 4

Running scans against random systems.

Always use:

* Your own lab
* CTF/lab targets
* Explicitly authorized systems
* Targets whose rules permit the testing activity

---

# 10. Interview Questions

### Q1. What is port 22?

Commonly SSH.

### Q2. What is port 443?

Commonly HTTPS.

### Q3. What is port 53?

DNS.

### Q4. What is port 445?

SMB.

### Q5. What is port 3389?

RDP.

### Q6. What is port 3306?

Commonly MySQL.

### Q7. What is port 5432?

Commonly PostgreSQL.

### Q8. Does port number guarantee the service?

No.

### Q9. What does Nmap `-sV` do?

Attempts service/version detection.

### Q10. What does "filtered" mean?

A filtering mechanism prevents Nmap from determining the port state normally.

---

# 11. Quick Memorization

```text
21   → FTP
22   → SSH
23   → Telnet
25   → SMTP
53   → DNS
67/68 → DHCP
80   → HTTP
110  → POP3
123  → NTP
143  → IMAP
161  → SNMP
389  → LDAP
443  → HTTPS
445  → SMB
636  → LDAPS
1433 → MSSQL
3306 → MySQL
3389 → RDP
5432 → PostgreSQL
5900 → VNC
```

---

# Final Mental Model

```text
IP Address
    ↓
Host
    ↓
Port
    ↓
Service
    ↓
Version
    ↓
Configuration
    ↓
Attack Surface
    ↓
Security Assessment
```

A professional pentester doesn't stop at:

> "Port 80 is open."

They ask:

> "What is running there, why is it exposed, how is it configured, and what security risk does that create?"

```

### Overall quality target

For this `01-Networking-Fundamentals` section, I'd rate this structure **9.7/10**.

The remaining **0.3** is not because the theory is missing; to reach true 10/10, the next step is **hands-on validation**: Wireshark packet analysis, TCP handshake observation, `ip route`/routing labs, subnet exercises, and an isolated Kali + Windows practice network.
```

[1]: https://github.com/JPKUMAWAT/CyberSecurity-VAPT-Networking-Pro-/tree/main/01-Networking-Fundamentals "CyberSecurity-VAPT-Networking-Pro-/01-Networking-Fundamentals at main · JPKUMAWAT/CyberSecurity-VAPT-Networking-Pro- · GitHub"
