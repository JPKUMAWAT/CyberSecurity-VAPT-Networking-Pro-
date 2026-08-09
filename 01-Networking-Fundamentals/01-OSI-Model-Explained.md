# OSI Model Explained — Beginner to VAPT Level

> **Goal:** Understand how data travels across a network and where common security tools and attacks operate.

---

## 1. What is the OSI Model?

**OSI** stands for **Open Systems Interconnection**.

The OSI model is a **7-layer conceptual model** used to understand how network communication works.

It divides network communication into seven layers:

```text
7 ─ Application
6 ─ Presentation
5 ─ Session
4 ─ Transport
3 ─ Network
2 ─ Data Link
1 ─ Physical
```

### Why was the OSI Model created?

Networking is complicated. Instead of treating communication as one huge process, the OSI model divides it into smaller responsibilities.

This helps us:

* Understand network communication
* Troubleshoot network problems
* Understand protocols
* Identify where a security control operates
* Communicate clearly with network/security teams

### Important

The OSI model is primarily a **conceptual/reference model**.

Real-world networks do not always map perfectly to exactly one OSI layer.

---

# 2. The 7 OSI Layers

| Layer | Name         | Main Responsibility                       | Examples               |
| ----: | ------------ | ----------------------------------------- | ---------------------- |
|     7 | Application  | Network services used by applications     | HTTP, DNS, FTP, SSH    |
|     6 | Presentation | Data representation, encoding, encryption | TLS concepts, encoding |
|     5 | Session      | Manages communication sessions            | Session management     |
|     4 | Transport    | End-to-end delivery                       | TCP, UDP               |
|     3 | Network      | Logical addressing and routing            | IP, ICMP               |
|     2 | Data Link    | Local network delivery                    | Ethernet, ARP, MAC     |
|     1 | Physical     | Signals and physical transmission         | Cables, radio, fiber   |

---

# 3. Easy Way to Remember the Layers

From **Layer 7 → Layer 1**:

> **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing

```text
7 Application
6 Presentation
5 Session
4 Transport
3 Network
2 Data Link
1 Physical
```

From **Layer 1 → Layer 7**:

> **P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way

---

# 4. Layer 1 — Physical

## What does it do?

The Physical layer deals with the actual transmission of **bits** over a physical or wireless medium.

It deals with things such as:

* Electrical signals
* Radio signals
* Light signals
* Cables
* Connectors
* Physical transmission characteristics

### Data Unit

```text
Bits
```

### Examples

* Ethernet cable
* Fiber optic cable
* Radio transmission
* Physical network interface

### Security Relevance

Physical security can involve:

* Unauthorized access to network equipment
* Cable tapping
* Device theft
* Rogue physical connections

### Simple Example

Imagine sending:

```text
10110101
```

The Physical layer is concerned with how those bits are actually represented and transmitted.

---

# 5. Layer 2 — Data Link

## What does it do?

The Data Link layer handles communication between devices on the **same local network/link**.

Important concepts include:

* MAC addresses
* Ethernet frames
* Switching
* Local delivery
* Error detection

### Data Unit

```text
Frame
```

### Important Address

```text
MAC Address
```

Example:

```text
00:1A:2B:3C:4D:5E
```

### Common Technologies/Protocols

* Ethernet
* Wi-Fi
* ARP

> **Important:** ARP is commonly associated with Layer 2 because it resolves IPv4 addresses to MAC addresses on a local network, although its exact OSI placement is sometimes discussed differently.

### Security Relevance

Layer 2 security topics include:

* ARP spoofing
* MAC spoofing
* VLAN-related attacks
* Rogue devices
* Switching attacks

### Example

Suppose:

```text
PC A → Switch → PC B
```

The switch primarily uses **MAC addresses** to make local forwarding decisions.

---

# 6. Layer 3 — Network

## What does it do?

The Network layer handles **logical addressing and routing between networks**.

### Data Unit

```text
Packet
```

### Important Address

```text
IP Address
```

Example:

```text
192.168.1.10
```

### Important Protocols

* IPv4
* IPv6
* ICMP
* IPsec

### Important Devices

* Routers
* Layer-3 switches

### Security Relevance

Many network-security activities involve Layer 3.

Examples:

* IP-based filtering
* Routing analysis
* Network segmentation
* ICMP analysis
* Network scanning

### Nmap Connection

When you perform a scan such as:

```bash
nmap 192.168.1.10
```

you are interacting with networking mechanisms involving Layer 3 and other layers depending on the scan.

---

# 7. Layer 4 — Transport

## What does it do?

The Transport layer provides **end-to-end transport between applications/processes**.

The two major protocols are:

```text
TCP
UDP
```

### TCP

TCP is:

* Connection-oriented
* Reliable
* Ordered
* Uses acknowledgements
* Provides retransmission

Example:

```text
HTTP
HTTPS
SSH
FTP
```

### TCP Three-Way Handshake

Conceptually:

```text
Client                  Server

   SYN  ────────────────>

        <──────── SYN-ACK

   ACK  ────────────────>
```

Connection established.

### UDP

UDP is:

* Connectionless
* Lightweight
* Does not provide TCP-style reliability
* Often useful where low overhead is important

Examples include:

* DNS
* DHCP
* Some streaming/real-time applications

### Data Unit

```text
TCP → Segment
UDP → Datagram
```

### Important Concept: Port

Ports help identify the destination application/service on a host.

Examples:

```text
22   → SSH
53   → DNS
80   → HTTP
443  → HTTPS
```

### VAPT Relevance

Port scanning is strongly associated with understanding Layer 4.

Example:

```bash
nmap -p 22,80,443 192.168.1.10
```

You are checking TCP services associated with those ports.

---

# 8. Layer 5 — Session

## What does it do?

The Session layer is concerned with establishing, managing, and terminating communication sessions.

It can involve concepts such as:

* Session establishment
* Session management
* Session termination
* Synchronization

### Important Reality

Modern networking stacks do not always implement the Session layer as a separate, clearly identifiable component.

Its responsibilities are often handled by application protocols and libraries.

### Security Relevance

Session-related security concepts include:

* Session management
* Session timeout
* Session fixation
* Session hijacking
* Authentication session handling

---

# 9. Layer 6 — Presentation

## What does it do?

The Presentation layer is concerned with **how data is represented**.

Typical responsibilities include:

* Encoding
* Decoding
* Serialization
* Data transformation
* Encryption/decryption concepts

Examples of data representations:

```text
UTF-8
JSON
XML
Base64
```

### Important Clarification

Protocols such as TLS can involve functions traditionally associated with the Presentation layer, but TLS does **not** cleanly belong to one OSI layer in every real-world implementation.

### Security Relevance

Security issues can involve:

* Encoding confusion
* Serialization vulnerabilities
* Improper data transformation
* Cryptographic implementation problems

---

# 10. Layer 7 — Application

## What does it do?

The Application layer provides network services used by applications.

Examples:

| Protocol | Common Purpose                  |
| -------- | ------------------------------- |
| HTTP     | Web communication               |
| HTTPS    | HTTP protected by TLS           |
| DNS      | Name resolution                 |
| FTP      | File transfer                   |
| SSH      | Secure remote administration    |
| SMTP     | Email transfer                  |
| DHCP     | Automatic network configuration |

### Data Unit

Often described as:

```text
Data
```

### VAPT Relevance

Web and application security heavily interacts with Layer 7.

Examples:

* SQL injection
* XSS
* Authentication issues
* Authorization flaws
* SSRF
* API vulnerabilities
* HTTP security misconfigurations

Tools commonly used include:

* Burp Suite
* OWASP ZAP
* curl
* Nikto
* ffuf

---

# 11. Complete Example — Opening a Website

Suppose you open:

```text
https://example.com
```

A simplified model is:

```text
Application
    ↓
Presentation
    ↓
Session
    ↓
Transport
    ↓
Network
    ↓
Data Link
    ↓
Physical
```

### What happens conceptually?

### Layer 7

The browser creates an HTTP request.

```text
GET / HTTP/1.1
```

### Layer 4

TCP provides transport.

```text
Source Port → Destination Port 443
```

### Layer 3

IP handles addressing.

```text
Your IP → Server IP
```

### Layer 2

The local network uses frames and MAC addressing.

### Layer 1

Bits are transmitted through:

* Wi-Fi
* Ethernet
* Fiber
* etc.

---

# 12. Encapsulation

When data travels **down** the OSI layers, each layer adds information needed for communication.

Conceptually:

```text
Application Data
       ↓
Transport Header + Data
       ↓
Network Header + Segment
       ↓
Data Link Header + Packet + Trailer
       ↓
Bits
```

Simplified:

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

This process is called:

# Encapsulation

At the receiving side, the process is reversed.

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

This is called:

# Decapsulation

---

# 13. OSI Layers and Addresses

One of the most important things to remember:

| Layer | Address/Identifier               |
| ----- | -------------------------------- |
| L2    | MAC address                      |
| L3    | IP address                       |
| L4    | Port number                      |
| L7    | Application/resource information |

Example:

```text
MAC → 00:1A:2B:3C:4D:5E
IP  → 192.168.1.10
Port → 443
Protocol → HTTPS
```

Don't confuse these.

---

# 14. OSI Model and Common Devices

| Device                   | Common Layer             |
| ------------------------ | ------------------------ |
| Hub                      | L1                       |
| Switch                   | L2                       |
| Router                   | L3                       |
| Firewall                 | L3/L4 and potentially L7 |
| Web Application Firewall | L7                       |

> These are simplified mappings. Modern security devices can operate across multiple layers.

---

# 15. OSI Model and Security Tools

| Tool       | Main Concepts Involved            |
| ---------- | --------------------------------- |
| Wireshark  | L2–L7 traffic analysis            |
| Nmap       | L3/L4/L7 depending on scan        |
| tcpdump    | Packet/traffic analysis           |
| Burp Suite | L7                                |
| curl       | L7                                |
| Scapy      | Multiple layers / packet crafting |
| Firewall   | Depends on implementation         |
| WAF        | L7                                |

### Important

Do not memorize:

> “Nmap = Layer 4.”

That is too simplistic.

Nmap can interact with multiple layers depending on the scan and protocol being examined.

---

# 16. OSI Model in VAPT

The OSI model helps a pentester ask:

> **“At which layer is this problem occurring?”**

### Example 1 — Open TCP Port

```text
Layer 4
```

Possible issue:

```text
Unnecessary exposed service
```

### Example 2 — Weak HTTP Security Header

```text
Layer 7
```

### Example 3 — ARP Spoofing

```text
Layer 2
```

### Example 4 — IP Routing Misconfiguration

```text
Layer 3
```

### Example 5 — Weak Physical Access

```text
Layer 1
```

---

# 17. Troubleshooting Using the OSI Model

The OSI model is also useful for troubleshooting.

Suppose a website isn't loading.

Check from lower layers upward:

```text
L1 → Is the physical/Wi-Fi connection working?
L2 → Is local network connectivity working?
L3 → Is IP addressing/routing working?
L4 → Is TCP connectivity working?
L5 → Is the session functioning?
L6 → Is data representation/encryption working?
L7 → Is the application/server responding?
```

This prevents random troubleshooting.

---

# 18. Common Beginner Mistakes

### Mistake 1

Thinking every protocol belongs perfectly to exactly one OSI layer.

**Reality:** Real protocols and implementations often cross OSI boundaries.

---

### Mistake 2

Thinking TCP = Layer 3.

Wrong.

```text
TCP → Layer 4
IP  → Layer 3
```

---

### Mistake 3

Thinking MAC address = IP address.

They are different.

```text
MAC → Layer 2
IP  → Layer 3
```

---

### Mistake 4

Thinking port numbers belong to IP.

Port numbers are associated with the **Transport layer**.

```text
IP → identifies host/interface
Port → identifies transport endpoint/service
```

---

### Mistake 5

Thinking HTTPS is simply “HTTP at Layer 6.”

Too simplistic.

HTTPS is HTTP carried over TLS, and TLS does not map perfectly to a single OSI layer.

---

# 19. OSI vs TCP/IP Model

### OSI

```text
7 Application
6 Presentation
5 Session
4 Transport
3 Network
2 Data Link
1 Physical
```

### TCP/IP Model

Common 4-layer representation:

```text
Application
Transport
Internet
Link
```

Approximate mapping:

| OSI             | TCP/IP      |
| --------------- | ----------- |
| L7 Application  | Application |
| L6 Presentation | Application |
| L5 Session      | Application |
| L4 Transport    | Transport   |
| L3 Network      | Internet    |
| L2 Data Link    | Link        |
| L1 Physical     | Link        |

The TCP/IP model is more closely related to the architecture used by the Internet.

---

# 20. Must-Know Protocol Mapping

| Protocol | Typical OSI Association |
| -------- | ----------------------: |
| Ethernet |                      L2 |
| ARP      |   L2 / boundary concept |
| IP       |                      L3 |
| ICMP     |                      L3 |
| TCP      |                      L4 |
| UDP      |                      L4 |
| DNS      |                      L7 |
| HTTP     |                      L7 |
| HTTPS    |                L7 + TLS |
| FTP      |                      L7 |
| SSH      |                      L7 |
| SMTP     |                      L7 |

---

# 21. Interview Questions

## Q1. What is the OSI model?

The OSI model is a seven-layer conceptual framework used to understand and standardize network communication.

---

## Q2. How many layers does OSI have?

**7 layers.**

---

## Q3. Which layer handles routing?

**Layer 3 — Network.**

---

## Q4. Which layer uses MAC addresses?

**Layer 2 — Data Link.**

---

## Q5. Which layer uses IP addresses?

**Layer 3 — Network.**

---

## Q6. Which layer uses port numbers?

**Layer 4 — Transport.**

---

## Q7. TCP belongs to which layer?

**Layer 4 — Transport.**

---

## Q8. IP belongs to which layer?

**Layer 3 — Network.**

---

## Q9. What is the TCP three-way handshake?

Conceptually:

```text
SYN
SYN-ACK
ACK
```

It establishes a TCP connection.

---

## Q10. What is encapsulation?

The process of adding protocol information as data moves down the networking stack.

---

## Q11. What is decapsulation?

The process of removing and processing protocol information as received data moves up the networking stack.

---

## Q12. Which layer is HTTP associated with?

**Layer 7 — Application.**

---

## Q13. Which layer is responsible for reliable TCP delivery?

**Layer 4 — Transport.**

---

## Q14. Which device commonly operates at Layer 2?

**Switch.**

---

## Q15. Which device commonly operates at Layer 3?

**Router.**

---

# 22. Quick Quiz

### Question 1

A MAC address is primarily associated with which OSI layer?

A. L1
B. L2
C. L3
D. L4

**Answer: B — Data Link**

---

### Question 2

Which protocol provides reliable, connection-oriented transport?

A. UDP
B. IP
C. TCP
D. ICMP

**Answer: C — TCP**

---

### Question 3

Which layer handles logical addressing?

A. Physical
B. Data Link
C. Network
D. Session

**Answer: C — Network**

---

### Question 4

Port 443 is primarily associated with which transport protocols?

A. TCP/UDP
B. Ethernet
C. ARP
D. ICMP

**Answer: A — TCP/UDP**

> The exact protocol depends on the service. HTTPS traditionally uses TCP 443; HTTP/3 uses QUIC over UDP 443.

---

### Question 5

What happens during encapsulation?

A. Data is deleted
B. Headers/trailers are added as data moves down the stack
C. IP is converted into MAC
D. TCP is removed

**Answer: B**

---

# 23. Practical VAPT Thinking

When you see a network problem, don't immediately run random tools.

Think:

```text
What layer is involved?
        ↓
What protocol is involved?
        ↓
What address/identifier is involved?
        ↓
What service is involved?
        ↓
What security impact could exist?
```

Example:

```text
Port 22 open
      ↓
TCP
      ↓
Layer 4
      ↓
SSH service
      ↓
Layer 7
      ↓
Check whether exposure is authorized
      ↓
Enumerate configuration/version safely
```

This is much better than simply memorizing:

```text
22 = SSH
```

---

# 24. Practical Commands for Learning

### Check local IP

```bash
ip addr
```

### Check routing

```bash
ip route
```

### Check connectivity

```bash
ping 8.8.8.8
```

### Check TCP ports on your authorized lab

```bash
nmap -sV <LAB-IP>
```

### Capture traffic

```bash
sudo tcpdump -i any
```

Use these only against systems/networks you own or are explicitly authorized to test.

---

# 25. Final Cheat Sheet

```text
L7 → Application  → HTTP, DNS, SSH, FTP
L6 → Presentation → Encoding, transformation, encryption concepts
L5 → Session      → Session management
L4 → Transport    → TCP, UDP, Ports
L3 → Network      → IP, ICMP, Routing
L2 → Data Link    → MAC, Ethernet, Frames
L1 → Physical     → Bits, Signals, Cables
```

### Remember This

```text
MAC  → L2
IP   → L3
PORT → L4
HTTP → L7
```

### Data Units

```text
L7–L5 → Data
L4    → Segment / Datagram
L3    → Packet
L2    → Frame
L1    → Bits
```

---

# 26. What You Should Be Able to Explain After This Lesson

You should be able to answer these without looking at notes:

* What is the OSI model?
* Why are there seven layers?
* Difference between MAC and IP?
* Difference between IP and port?
* TCP vs UDP?
* What happens during a TCP handshake?
* What is encapsulation?
* What is decapsulation?
* Which layer handles routing?
* Which layer handles ports?
* Where does HTTP operate?
* Why can one security tool interact with multiple OSI layers?
* How can the OSI model help troubleshoot a network?
* How does the OSI model help a VAPT professional?

---

# Key Takeaway

Don't learn the OSI model only as:

> **“7 layers to memorize for an exam.”**

Learn it as a **mental map of network communication**.

When you perform VAPT, think:

```text
Physical
   ↓
Local Network
   ↓
IP / Routing
   ↓
TCP / UDP / Ports
   ↓
Protocol
   ↓
Application
   ↓
Security Issue
```

Once this becomes natural, tools such as **Nmap, Wireshark, Burp Suite, Scapy, and Python socket programming** become much easier to understand because you know what is actually happening underneath the tool.
