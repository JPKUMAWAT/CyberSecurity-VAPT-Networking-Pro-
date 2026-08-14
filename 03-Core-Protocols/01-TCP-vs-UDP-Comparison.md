

# 1. What Are TCP and UDP?

TCP and UDP are **Transport Layer protocols**.

In the OSI model:

```text
Layer 7  Application
Layer 6  Presentation
Layer 5  Session
Layer 4  ← TCP / UDP
Layer 3  IP
Layer 2  Ethernet / Wi-Fi
Layer 1  Physical
```

In the TCP/IP model:

```text
Application
    ↓
Transport ← TCP / UDP
    ↓
Internet ← IP
    ↓
Network Access
```

Their primary job is to provide **communication between applications/processes** running on different systems.

---

# 2. Why Do We Need TCP/UDP?

Imagine your computer is communicating with a server:

```text
Your Computer
IP: 192.168.1.10
      |
      | Network
      ↓
Server
IP: 192.168.1.20
```

IP can deliver packets between hosts.

But the operating system also needs to know:

> **Which application should receive this traffic?**

That's where **ports** come in.

```text
IP address → Identifies the host
Port       → Identifies the transport endpoint/application
```

Example:

```text
192.168.1.20:443
        │      │
        │      └── Port
        └───────── IP
```

TCP or UDP carries this transport information.

---

# 3. TCP — Transmission Control Protocol

**TCP = Transmission Control Protocol**

TCP is:

* Connection-oriented
* Reliable
* Ordered
* Byte-stream based
* Error-detecting
* Flow-controlled
* Congestion-aware

TCP is designed when the application generally needs dependable delivery.

---

# 4. UDP — User Datagram Protocol

**UDP = User Datagram Protocol**

UDP is:

* Connectionless
* Message/datagram oriented
* Lightweight
* Low-overhead
* No built-in retransmission
* No built-in ordering
* No built-in congestion control at the transport layer

UDP does **not** mean "bad" or "unreliable Internet communication."

It simply provides fewer transport-layer guarantees than TCP.

Applications can implement their own reliability mechanisms when required.

---

# 5. TCP vs UDP — Quick Comparison

| Feature             | TCP                               | UDP                                       |
| ------------------- | --------------------------------- | ----------------------------------------- |
| Full name           | Transmission Control Protocol     | User Datagram Protocol                    |
| Connection          | Connection-oriented               | Connectionless                            |
| Reliability         | Built-in                          | Not built-in                              |
| Ordering            | Yes                               | No                                        |
| Retransmission      | Yes                               | No                                        |
| Flow control        | Yes                               | No                                        |
| Congestion control  | Yes                               | No                                        |
| Data model          | Byte stream                       | Datagrams                                 |
| Header size         | Minimum 20 bytes                  | 8 bytes                                   |
| Overhead            | Higher                            | Lower                                     |
| Speed/latency       | Usually more overhead             | Usually lower overhead                    |
| Handshake           | TCP handshake                     | No TCP-style handshake                    |
| Broadcast/multicast | Not used as a transport mechanism | Commonly used with IP multicast/broadcast |
| Typical use         | Web, SSH, FTP                     | DNS, DHCP, streaming/real-time protocols  |

---

# 6. The Most Important Difference

Remember this:

```text
TCP
↓
"Make sure the data arrives correctly and in order."

UDP
↓
"Send this datagram without TCP's delivery guarantees."
```

This is a simplified mental model, but it is excellent for beginners.

---

# 7. TCP Connection-Oriented Communication

Before normal TCP application data is exchanged, TCP establishes a connection.

Simplified:

```text
Client                         Server
  |                              |
  | -------- SYN --------------> |
  |                              |
  | <------ SYN + ACK ---------- |
  |                              |
  | -------- ACK --------------> |
  |                              |
  |       Connection ready       |
```

This is called the:

# TCP Three-Way Handshake

You'll study it in depth in:

```text
02-TCP-Three-Way-Handshake.md
```

---

# 8. UDP Communication

UDP doesn't perform the TCP three-way handshake.

Simplified:

```text
Client                         Server
  |                              |
  | -------- UDP Datagram -----> |
  |                              |
```

The application can send a datagram without first establishing a TCP-style connection.

However, network devices may still perform other communication or filtering around that traffic.

---

# 9. TCP Reliability

TCP provides several mechanisms that help reliable communication.

Important concepts:

* Sequence numbers
* Acknowledgements
* Retransmissions
* Checksum
* Receive window
* Flow control
* Congestion control

Example:

```text
Sender                         Receiver

Segment #1 -------------------->
             <---------------- ACK

Segment #2 -------------------->
             <---------------- ACK
```

If a segment appears to be lost, TCP can retransmit it.

---

# 10. TCP Ordering

Suppose data is divided into segments:

```text
1
2
3
4
```

They might encounter different delays in the network.

TCP uses sequence numbers to allow the receiver to reconstruct the byte stream correctly.

Conceptually:

```text
Network:

1 ────────>
3 ────────>
2 ────────>
4 ────────>

TCP receiver:

1 → 2 → 3 → 4
```

The application sees an ordered byte stream.

---

# 11. UDP Does Not Guarantee Ordering

UDP datagrams can arrive:

```text
1
3
2
4
```

or some may never arrive.

UDP itself doesn't automatically:

* Retransmit missing datagrams
* Reorder them
* Establish a connection

If an application needs those features, it can implement them separately.

---

# 12. TCP Is a Byte Stream

This is a **very important technical distinction**.

TCP does not preserve application message boundaries.

Suppose an application sends:

```text
HELLO
WORLD
```

TCP delivers a continuous byte stream.

The receiving application must know how to interpret message boundaries.

For example:

```text
HELLOWORLD
```

could arrive as:

```text
HEL
LOWOR
LD
```

or another segmentation.

Therefore:

> **One `send()` does not necessarily equal one `recv()`.**

This is an important concept when writing Python network programs.

---

# 13. UDP Is Datagram-Based

UDP preserves datagram boundaries.

Conceptually:

```text
send(datagram A)
send(datagram B)
```

The receiver receives:

```text
datagram A
datagram B
```

assuming they arrive and are accepted.

Each UDP datagram is an individual message at the UDP layer.

---

# 14. TCP Header

A TCP header has a **minimum size of 20 bytes**.

Important fields include:

```text
Source Port
Destination Port
Sequence Number
Acknowledgment Number
Header Length
Flags
Window Size
Checksum
Urgent Pointer
Options
```

Important TCP flags:

```text
SYN
ACK
FIN
RST
PSH
URG
ECE
CWR
```

The most important for beginners:

| Flag | Basic purpose                          |
| ---- | -------------------------------------- |
| SYN  | Start/synchronize TCP connection       |
| ACK  | Acknowledge received data/control      |
| FIN  | Graceful connection termination        |
| RST  | Reset/abort a connection               |
| PSH  | Request prompt delivery to application |
| URG  | Indicates urgent pointer significance  |

---

# 15. UDP Header

UDP has a very small **8-byte header**.

Fields:

```text
Source Port
Destination Port
Length
Checksum
```

That's one reason UDP has less transport-layer overhead than TCP.

---

# 16. TCP Header vs UDP Header

```text
TCP
┌─────────────────────────────┐
│ Source Port                 │
│ Destination Port            │
│ Sequence Number             │
│ Acknowledgment Number       │
│ Flags                       │
│ Window                      │
│ Checksum                    │
│ Options ...                 │
└─────────────────────────────┘
Minimum: 20 bytes


UDP
┌─────────────────────────────┐
│ Source Port                 │
│ Destination Port            │
│ Length                      │
│ Checksum                    │
└─────────────────────────────┘
8 bytes
```

---

# 17. Common TCP Protocols

Many familiar protocols commonly use TCP.

| Protocol    | Typical Port | Transport |
| ----------- | -----------: | --------- |
| HTTP        |           80 | TCP       |
| HTTPS       |          443 | TCP       |
| SSH         |           22 | TCP       |
| FTP control |           21 | TCP       |
| SMTP        |           25 | TCP       |
| IMAP        |          143 | TCP       |
| POP3        |          110 | TCP       |
| SMB         |          445 | TCP       |
| RDP         |         3389 | TCP       |
| MySQL       |         3306 | TCP       |
| PostgreSQL  |         5432 | TCP       |

**Important:** Port numbers are conventions, not guarantees of the actual protocol.

A service can be configured to listen on a different port.

---

# 18. Common UDP Protocols

| Protocol | Typical Port | Transport    |
| -------- | -----------: | ------------ |
| DNS      |           53 | UDP commonly |
| DHCP     |        67/68 | UDP          |
| TFTP     |           69 | UDP          |
| SNMP     |          161 | UDP          |
| NTP      |          123 | UDP          |
| Syslog   |          514 | UDP commonly |

Some protocols can use **both TCP and UDP**, depending on the operation/version/configuration.

DNS is a classic example.

---

# 19. DNS: TCP or UDP?

A common beginner question:

> Is DNS TCP or UDP?

### Answer:

**Usually UDP**, commonly port `53`.

But DNS can also use **TCP**, including situations such as:

* Large responses
* Zone transfers
* Protocol-specific fallback/requirements

Modern DNS deployments may also use DNS over HTTPS or DNS over TLS, which changes the transport/application path.

Therefore:

```text
DNS ≠ always UDP
```

---

# 20. TCP vs UDP and Speed

You will often hear:

> "UDP is faster than TCP."

That's an oversimplification.

Better statement:

> **UDP has less transport-layer overhead and does not perform TCP's connection establishment, retransmission, ordering, flow-control, and congestion-control mechanisms.**

This can make UDP useful for latency-sensitive applications.

But actual application performance depends on:

* Network conditions
* Application design
* Packet loss
* Congestion
* Protocol implementation
* Encryption
* Server/client behavior

---

# 21. Why Use UDP?

UDP is useful when an application values:

* Low overhead
* Low latency
* Simple request/response
* Real-time communication
* Ability to tolerate some packet loss
* Application-controlled reliability

Examples include:

```text
DNS
DHCP
NTP
VoIP
Online gaming
Real-time media
```

---

# 22. Why Use TCP?

TCP is useful when applications need:

* Reliable delivery
* Ordered data
* Retransmission
* Flow control
* Congestion control
* A continuous byte stream

Examples:

```text
HTTPS
SSH
FTP
SMTP
IMAP
SMB
```

---

# 23. TCP Connection Termination

TCP normally uses a graceful closing process involving `FIN` and `ACK`.

Simplified:

```text
Client                         Server
  |                              |
  | -------- FIN -------------> |
  | <-------- ACK ------------- |
  |                              |
  | <-------- FIN ------------- |
  | -------- ACK -------------> |
  |                              |
  Connection closed
```

This is different from the three-way handshake used to establish the connection.

---

# 24. TCP RST

`RST` means **Reset**.

It can be used when a connection needs to be aborted or when traffic is sent to a port where no suitable TCP listener exists.

Conceptually:

```text
Client
  |
  | TCP connection attempt
  v
Server
  |
  | RST
  v
Client
```

In packet analysis, seeing RST packets can provide useful information about connection behavior.

---

# 25. TCP and VAPT

TCP knowledge is essential during network penetration testing.

A typical authorized assessment might look like:

```text
Target
   |
   ↓
Port Discovery
   |
   ↓
Open TCP Ports
   |
   ↓
Service Detection
   |
   ↓
Version Enumeration
   |
   ↓
Vulnerability Assessment
```

For example:

```text
22/tcp  → SSH
80/tcp  → HTTP
443/tcp → HTTPS
445/tcp → SMB
```

Each exposed service becomes part of the **attack surface**.

---

# 26. UDP and VAPT

UDP services are also important.

Example:

```text
53/udp  → DNS
161/udp → SNMP
123/udp → NTP
```

A common beginner mistake is:

> "I scanned TCP, so I scanned the network."

❌ Wrong.

TCP and UDP are different transport protocols.

A complete authorized assessment may need to consider both.

---

# 27. TCP/UDP Port Scanning

Nmap can perform TCP and UDP scanning.

Example against an authorized lab target:

```bash
nmap <authorized-target>
```

UDP scan:

```bash
nmap -sU <authorized-target>
```

TCP SYN scan:

```bash
nmap -sS <authorized-target>
```

Service detection:

```bash
nmap -sV <authorized-target>
```

Remember:

```text
-sS → TCP SYN scan
-sU → UDP scan
-sV → Service/version detection
```

---

# 28. Why UDP Scanning Is Different

UDP has no TCP-style handshake.

Therefore determining whether a UDP port is open can be more difficult.

Possible outcomes can include:

```text
open
closed
open|filtered
```

Depending on the response and scanning technique.

This is one reason UDP scanning can take longer than many TCP scans.

---

# 29. TCP Connection Example

Imagine:

```text
Client:
192.168.1.10:50000

Server:
192.168.1.20:443
```

The connection is identified using endpoint information.

Conceptually:

```text
192.168.1.10:50000
        ↓
      TCP
        ↓
192.168.1.20:443
```

The source port is often an ephemeral port chosen by the operating system.

---

# 30. The 4-Tuple

A TCP connection can be identified by:

```text
Source IP
Source Port
Destination IP
Destination Port
```

Example:

```text
192.168.1.10
50000
192.168.1.20
443
```

Together:

```text
(192.168.1.10, 50000,
 192.168.1.20, 443)
```

This is called the **4-tuple**.

It is extremely useful for understanding:

* Connections
* Firewalls
* NAT
* Packet captures
* Network troubleshooting

---

# 31. TCP Flow Control

TCP has **flow control** to prevent a sender from overwhelming the receiver.

Simplified:

```text
Sender
   |
   | Data
   ↓
Receiver
   |
   | "I can currently handle X"
   ↓
Sender adjusts sending
```

The TCP receive window plays an important role.

---

# 32. TCP Congestion Control

Flow control and congestion control are **not the same thing**.

### Flow Control

Protects the:

```text
Receiver
```

### Congestion Control

Helps manage:

```text
Network congestion
```

Remember:

```text
Flow control → Receiver capacity
Congestion control → Network capacity
```

---

# 33. TCP Retransmission

If TCP determines that data hasn't been successfully acknowledged within the appropriate timing behavior, it can retransmit.

Simplified:

```text
Sender                Receiver

Segment 1 ---------->

ACK <----------------

Segment 2 ----------> X lost

             no ACK

Segment 2 ---------->  retry
```

This contributes to TCP reliability.

---

# 34. UDP Checksum

UDP includes a checksum for detecting certain transmission errors.

Therefore:

> UDP is **not** completely without error detection.

The important distinction is:

```text
UDP:
Checksum ✔
Automatic retransmission ✘
Ordering guarantee ✘
Connection establishment ✘
```

---

# 35. Security Comparison

Neither TCP nor UDP is inherently "secure."

Security depends on the protocol and surrounding controls.

| Area              | TCP                       | UDP                                      |
| ----------------- | ------------------------- | ---------------------------------------- |
| Encryption        | Not built into TCP itself | Not built into UDP itself                |
| Authentication    | Depends on application    | Depends on application                   |
| Spoofing concerns | Possible in some contexts | Often easier in connectionless protocols |
| DoS exposure      | Yes                       | Yes                                      |
| Scanning          | Common                    | Often more difficult                     |
| Firewall rules    | Can filter TCP            | Can filter UDP                           |

---

# 36. TCP Security Concerns

Examples of security topics involving TCP:

* SYN flooding
* Connection exhaustion
* Port scanning
* TCP reset attacks
* Session-related attacks
* Service exposure

Modern systems use multiple mitigations, so the existence of a TCP-related attack does not automatically mean a system is vulnerable.

---

# 37. UDP Security Concerns

UDP can have security implications involving:

* Spoofed source addresses
* Reflection/amplification attacks
* UDP flooding
* Exposed UDP services
* Weak service authentication
* Information leakage

Examples of historically abused services include poorly configured:

```text
DNS
NTP
SNMP
```

---

# 38. TCP SYN Flood — Concept

A SYN flood attempts to consume server resources by generating many TCP connection attempts without completing normal connection establishment.

Conceptually:

```text
Attacker
  |
  | SYN
  | SYN
  | SYN
  | SYN
  v
Server
```

Modern systems can use defenses such as:

* SYN cookies
* Connection limits
* Firewalls
* Load balancers
* Rate limiting
* Network-level filtering

---

# 39. UDP Reflection/Amplification — Concept

Some UDP services can be abused in reflection/amplification attacks.

Simplified:

```text
Attacker
   |
   | Spoofed request
   v
UDP Service
   |
   | Larger response
   v
Victim
```

This is one reason exposed UDP services should be configured carefully.

---

# 40. TCP vs UDP in Wireshark

When analyzing traffic, useful filters include:

```text
tcp
```

and:

```text
udp
```

For a specific TCP port:

```text
tcp.port == 443
```

For UDP:

```text
udp.port == 53
```

For TCP SYN packets:

```text
tcp.flags.syn == 1
```

For TCP reset packets:

```text
tcp.flags.reset == 1
```

These are useful for learning packet behavior in your own lab.

---

# 41. Practical Lab — TCP

Use your own machine or authorized lab.

### Check listening TCP services on Linux

```bash
ss -lnt
```

### More detailed TCP sockets

```bash
ss -lntp
```

Typical fields include:

```text
Local Address:Port
Peer Address:Port
State
```

---

# 42. Practical Lab — UDP

List UDP sockets:

```bash
ss -lun
```

More details:

```bash
ss -lunp
```

Now compare:

```bash
ss -lntp
ss -lunp
```

Ask yourself:

> Which services are listening over TCP?

> Which services are listening over UDP?

This is a useful networking foundation for VAPT.

---

# 43. Python TCP Example

A basic authorized-lab TCP client:

```python
import socket

host = "127.0.0.1"
port = 8000

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.settimeout(3)
    s.connect((host, port))
    print("[+] TCP connection established")
```

Important:

```text
AF_INET
    ↓
IPv4

SOCK_STREAM
    ↓
TCP
```

---

# 44. Python UDP Example

```python
import socket

host = "127.0.0.1"
port = 9999

with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as s:
    s.sendto(b"Hello", (host, port))
    print("[+] UDP datagram sent")
```

Important:

```text
SOCK_DGRAM
    ↓
UDP
```

Practice these against your own local lab rather than arbitrary Internet systems.

---

# 45. Real-World Architecture Example

Consider a web application:

```text
                 Internet
                    |
                    v
             Firewall / WAF
                    |
                    v
              Web Server
             TCP 443/HTTPS
                    |
                    v
             Application
                    |
                    v
             Database
              TCP 3306
```

TCP is commonly used because web/database communication typically benefits from reliable ordered delivery.

---

# 46. Real-Time Application Example

Consider a real-time voice application:

```text
User A
   |
   | UDP
   v
Internet
   |
   v
User B
```

Some applications prefer UDP because late data can be less useful than maintaining low latency.

For example:

```text
Old audio packet arrives late
        ↓
Maybe discard it
        ↓
Continue with current audio
```

A retransmission delay may be undesirable for some real-time applications.

---

# 47. TCP vs UDP — Decision Framework

Ask:

### Does the application need reliable ordered delivery?

```text
YES → TCP may be appropriate
```

### Does it prioritize low overhead/latency and can tolerate loss or implement its own reliability?

```text
YES → UDP may be appropriate
```

But final protocol choice depends on application requirements.

---

# 48. Common Beginner Mistakes

### ❌ Mistake 1

"TCP is secure and UDP is insecure."

### ✅ Correct

Neither provides application-layer encryption by itself.

---

### ❌ Mistake 2

"UDP doesn't have a checksum."

### ✅ Correct

UDP has a checksum.

---

### ❌ Mistake 3

"UDP is always faster."

### ✅ Correct

UDP has less protocol overhead, but real performance depends on the application and network.

---

### ❌ Mistake 4

"Port 443 always means HTTPS."

### ✅ Correct

Port numbers are conventions. A service can run elsewhere.

---

### ❌ Mistake 5

"DNS only uses UDP."

### ✅ Correct

DNS commonly uses UDP but can also use TCP and newer encrypted DNS transports use other mechanisms.

---

### ❌ Mistake 6

"TCP sends one message per packet."

### ✅ Correct

TCP provides a **byte stream**, not message boundaries.

---

### ❌ Mistake 7

"Scanning TCP means all services have been discovered."

### ✅ Correct

UDP services can exist too.

---

# 49. Interview Questions

### Beginner

**Q1. What is TCP?**

A connection-oriented transport protocol that provides reliable, ordered byte-stream delivery.

**Q2. What is UDP?**

A connectionless, datagram-oriented transport protocol with minimal transport-layer overhead and no built-in delivery/order guarantees.

**Q3. Which layer do TCP and UDP operate at?**

Transport Layer / Layer 4.

**Q4. What is the minimum TCP header size?**

20 bytes.

**Q5. What is the UDP header size?**

8 bytes.

---

### Intermediate

**Q6. Why does TCP use sequence numbers?**

To identify data positions and support ordered delivery and retransmission.

**Q7. What is the TCP three-way handshake?**

```text
SYN
SYN-ACK
ACK
```

**Q8. What is the difference between flow control and congestion control?**

Flow control protects the receiver; congestion control responds to network capacity/congestion.

**Q9. What is a TCP RST?**

A reset used to abruptly terminate or reject a TCP connection.

**Q10. Why can UDP scanning be difficult?**

UDP has no TCP-style handshake, and many UDP services may not respond to arbitrary probes.

---

# 50. VAPT Scenario Questions

### Scenario 1

Nmap reports:

```text
22/tcp open ssh
80/tcp open http
443/tcp open https
```

What does this tell you?

**Answer:**

These TCP ports are reachable/open and have services associated with them. They represent potential attack-surface areas for further authorized enumeration.

---

### Scenario 2

A TCP scan shows no interesting ports, but the application is known to use DNS.

What should you consider?

**Answer:**

UDP services may need assessment too. DNS commonly uses UDP port 53.

---

### Scenario 3

You see:

```text
SYN
SYN-ACK
ACK
```

What does it indicate?

**Answer:**

A TCP connection establishment completed successfully.

---

# 51. MCQs

### 1. TCP and UDP operate primarily at which layer?

A. Layer 2
B. Layer 3
C. Layer 4
D. Layer 7

**Answer: C**

---

### 2. Which protocol is connection-oriented?

A. UDP
B. TCP
C. IP
D. ARP

**Answer: B**

---

### 3. Which protocol uses a three-way handshake?

A. UDP
B. TCP
C. DNS
D. ARP

**Answer: B**

---

### 4. What is the minimum TCP header size?

A. 8 bytes
B. 12 bytes
C. 20 bytes
D. 32 bytes

**Answer: C**

---

### 5. What is the UDP header size?

A. 8 bytes
B. 16 bytes
C. 20 bytes
D. 32 bytes

**Answer: A**

---

### 6. Which TCP flag starts connection establishment?

A. ACK
B. FIN
C. SYN
D. RST

**Answer: C**

---

### 7. Which flag is associated with connection reset?

A. SYN
B. RST
C. FIN
D. PSH

**Answer: B**

---

### 8. Which protocol is commonly used by DNS?

A. TCP only
B. UDP only
C. UDP commonly, with TCP also used
D. ICMP

**Answer: C**

---

### 9. Which command shows TCP listening sockets on Linux?

A. `ss -lnt`
B. `ip route`
C. `arp -a`
D. `ps aux`

**Answer: A**

---

### 10. Which Nmap option requests UDP scanning?

A. `-sS`
B. `-sV`
C. `-sU`
D. `-O`

**Answer: C**

---

### 11. Which statement about TCP is correct?

A. TCP is a datagram protocol
B. TCP guarantees message boundaries
C. TCP provides an ordered byte stream
D. TCP has no retransmission mechanism

**Answer: C**

---

### 12. Which is a typical UDP service?

A. SSH
B. HTTPS
C. DNS
D. FTP

**Answer: C**

---

### 13. Which is a typical TCP service?

A. SSH
B. DHCP
C. NTP
D. TFTP

**Answer: A**

---

### 14. What does `SOCK_STREAM` normally represent in Python?

A. UDP
B. TCP
C. ICMP
D. DNS

**Answer: B**

---

### 15. What does `SOCK_DGRAM` normally represent?

A. TCP
B. UDP
C. HTTP
D. SSH

**Answer: B**

---

# 52. Must-Memorize Cheat Sheet

```text
TCP
├── Connection-oriented
├── Reliable
├── Ordered
├── Byte stream
├── Retransmission
├── Flow control
├── Congestion control
└── 3-way handshake

UDP
├── Connectionless
├── Datagram-based
├── No built-in retransmission
├── No built-in ordering
├── Low overhead
└── 8-byte header
```

### TCP Handshake

```text
SYN
 ↓
SYN-ACK
 ↓
ACK
```

### TCP Close

```text
FIN
 ↓
ACK
 ↓
FIN
 ↓
ACK
```

### Common TCP

```text
22   SSH
80   HTTP
443  HTTPS
445  SMB
3389 RDP
3306 MySQL
```

### Common UDP

```text
53   DNS
67/68 DHCP
123  NTP
161  SNMP
514  Syslog commonly
```

### Python

```text
SOCK_STREAM → TCP
SOCK_DGRAM  → UDP
```

### Nmap

```text
-sS → TCP SYN scan
-sU → UDP scan
-sV → Service/version detection
```

---

# 53. Practical Mini-Project

## TCP vs UDP Service Mapper

Build a small **local-lab** Python utility that:

```text
1. Accepts localhost as target
       ↓
2. Tests a predefined list of TCP ports
       ↓
3. Tests a predefined list of UDP ports
       ↓
4. Separates results
       ↓
5. Saves results to results.txt
```

Expected output:

```text
=== TCP ===
22    closed
80    open
443   open

=== UDP ===
53    no-response
123   no-response
```

### Skills gained

* Python sockets
* TCP
* UDP
* Ports
* Timeouts
* Error handling
* File handling
* Basic network enumeration

Keep this to systems you own or have explicit permission to test.

---

# 54. Final Mental Model

Think of the network stack like this:

```text
Application
     |
     | "I need to communicate"
     v
TCP / UDP
     |
     | "Which application/port?"
     v
IP
     |
     | "Which host?"
     v
Ethernet/Wi-Fi
     |
     | "How do I reach the next hop?"
     v
Network
```

And remember the fundamental difference:

```text
                    TCP              UDP
                     │                 │
                     ↓                 ↓
              Connection?        Connectionless
                     │                 │
                     ↓                 ↓
               Reliable?          Built-in reliability?
                     │                 │
                    YES                NO
                     │                 │
                     ↓                 ↓
                Ordered             No ordering
                     │                 │
                     ↓                 ↓
              Byte stream          Datagrams
```

## Final Mental Shortcut

> **TCP = dependable ordered byte stream with connection management.**
> **UDP = lightweight datagrams without TCP's built-in delivery/order guarantees.**

