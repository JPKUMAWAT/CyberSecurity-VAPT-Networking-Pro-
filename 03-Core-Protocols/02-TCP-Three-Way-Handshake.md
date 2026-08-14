

# 1. What Is the TCP Three-Way Handshake?

The **TCP Three-Way Handshake** is the process used by TCP to establish a reliable connection between a **client** and a **server** before application data is exchanged.

It consists of three main messages:

```text
1. SYN
2. SYN-ACK
3. ACK
```

### Simple idea

Think of a phone call:

```text
Client: "Can you hear me?"       → SYN
Server: "Yes, I can hear you."   → SYN-ACK
Client: "Great, I can hear you." → ACK

Connection established.
```

---

# 2. Why Does TCP Need a Handshake?

TCP is **connection-oriented**.

Before sending normal application data, both sides need to establish the connection and synchronize important TCP state.

The handshake helps establish:

* Initial Sequence Numbers
* Bidirectional communication
* Connection state
* Reliability parameters
* That both endpoints are reachable at the TCP layer

### Important

TCP does **not** guarantee that the application itself is available or healthy.

For example:

```text
TCP connection succeeds
        ↓
Port is reachable
        ↓
Application may still be broken
```

So:

> **Open TCP port ≠ vulnerable service ≠ healthy application**

This distinction is important in VAPT.

---

# 3. TCP Connection States

A TCP endpoint moves through different states during connection establishment.

Common states:

| State          | Meaning                              |
| -------------- | ------------------------------------ |
| `CLOSED`       | No connection                        |
| `LISTEN`       | Server waiting for connections       |
| `SYN-SENT`     | Client sent SYN                      |
| `SYN-RECEIVED` | Server received SYN and sent SYN-ACK |
| `ESTABLISHED`  | Connection successfully established  |
| `FIN-WAIT`     | Connection is being closed           |
| `TIME-WAIT`    | Waiting before fully closing         |

For the three-way handshake, the important states are:

```text
Client                         Server

CLOSED                         LISTEN
   |                              |
   | -------- SYN -------------> |
   |                              |
SYN-SENT                    SYN-RECEIVED
   |                              |
   | <------ SYN + ACK ---------- |
   |                              |
   | -------- ACK -------------> |
   |                              |
ESTABLISHED                 ESTABLISHED
```

---

# 4. Step-by-Step Handshake

## Step 1 — SYN

The client starts the connection by sending a TCP segment with the **SYN flag** set.

```text
Client → Server

SYN
Seq = X
```

Example:

```text
Client
192.168.1.10:50000
        |
        | SYN
        | Seq = 1000
        ↓
Server
192.168.1.20:80
```

### What does SYN mean?

**SYN = Synchronize**

It tells the server:

> "I want to establish a TCP connection, and here is my initial sequence number."

The client enters:

```text
SYN-SENT
```

---

# 5. Step 2 — SYN-ACK

If the server accepts the connection request, it responds with:

```text
SYN + ACK
```

Example:

```text
Server → Client

SYN
ACK

Seq = 5000
Ack = 1001
```

Why `Ack = 1001`?

Because the server is acknowledging the client's SYN.

Conceptually:

```text
Client sent:
Seq = 1000

Server acknowledges:
Ack = 1001
```

The server's own sequence number starts at `5000`.

The server enters:

```text
SYN-RECEIVED
```

---

# 6. Step 3 — ACK

The client responds with an ACK.

```text
Client → Server

ACK
Seq = 1001
Ack = 5001
```

Now both sides know that the connection has been established.

```text
Client                         Server
ESTABLISHED                    ESTABLISHED
```

Application data can now be exchanged.

---

# 7. Complete Sequence Number Example

Let's put everything together.

```text
CLIENT                              SERVER

Seq = 1000
   |
   | -------- SYN ----------------> |
   |                               |
   |                               |
   | <------- SYN + ACK ----------- |
   |          Seq = 5000            |
   |          Ack = 1001            |
   |                               |
   | -------- ACK ----------------> |
   |          Seq = 1001           |
   |          Ack = 5001            |
   |                               |
   |                               |
   | ===== CONNECTION READY ====== |
```

### Remember

If:

```text
Client SYN Seq = X
```

Server normally acknowledges it with:

```text
ACK = X + 1
```

Similarly, if:

```text
Server SYN Seq = Y
```

Client acknowledges:

```text
ACK = Y + 1
```

---

# 8. What Are TCP Flags?

TCP contains several control flags.

Important ones:

| Flag  | Meaning        | Common purpose                          |
| ----- | -------------- | --------------------------------------- |
| `SYN` | Synchronize    | Start connection                        |
| `ACK` | Acknowledgment | Confirm received data/control           |
| `FIN` | Finish         | Graceful connection termination         |
| `RST` | Reset          | Immediately terminate/refuse connection |
| `PSH` | Push           | Request prompt delivery to application  |
| `URG` | Urgent         | Indicates urgent-pointer usage          |

For the three-way handshake:

```text
SYN
 ↓
SYN + ACK
 ↓
ACK
```

---

# 9. TCP Handshake vs Data Transfer

Don't confuse these stages.

### Connection establishment

```text
SYN
SYN-ACK
ACK
```

### Data transfer

```text
Client → HTTP Request
Server → HTTP Response
```

Example:

```text
TCP Handshake
      ↓
Connection established
      ↓
HTTP request
      ↓
HTTP response
```

This is extremely important when analyzing packets in Wireshark.

---

# 10. TCP Handshake and Ports

A TCP connection is identified using a combination commonly called the **4-tuple**:

```text
Source IP
Source Port
Destination IP
Destination Port
```

Example:

```text
192.168.1.10:51520
        ↓
192.168.1.20:443
```

The client usually uses a temporary **ephemeral source port**.

The server listens on a known destination port.

Example:

```text
Client                         Server
192.168.1.10:51520
        |
        | SYN
        ↓
192.168.1.20:443
```

Here:

```text
51520 = client ephemeral port
443   = HTTPS service port
```

---

# 11. Why Does the Client Use an Ephemeral Port?

A single machine may communicate with many services simultaneously.

For example:

```text
Client
├── :51001 → Server A :443
├── :51002 → Server B :443
├── :51003 → Server C :22
└── :51004 → Server D :80
```

The different source ports help the operating system distinguish connections.

---

# 12. TCP Handshake in Real Networking

Suppose you open:

```text
https://example.com
```

A simplified flow is:

```text
Browser
   |
   | DNS lookup
   ↓
IP address obtained
   |
   | TCP SYN
   ↓
Server :443
   |
   | SYN-ACK
   ↓
Browser
   |
   | ACK
   ↓
TCP connection established
   |
   | TLS handshake
   ↓
HTTPS communication
```

Important:

> TCP handshake and TLS handshake are **different processes**.

---

# 13. TCP Handshake vs TLS Handshake

### TCP handshake

Establishes:

```text
TCP connection
```

Sequence:

```text
SYN
SYN-ACK
ACK
```

### TLS handshake

Establishes:

```text
Secure encrypted communication
```

It occurs **after TCP connection establishment** in traditional TCP-based HTTPS.

Simplified:

```text
TCP Handshake
      ↓
TLS Handshake
      ↓
Encrypted HTTP
```

---

# 14. What Happens When a Port Is Closed?

Suppose you connect to:

```text
192.168.1.20:9999
```

and nothing is listening.

A common response is:

```text
Client → SYN
Client ← RST-ACK
```

Conceptually:

```text
Client                  Server

SYN -------------------->
    <------------------- RST
```

This tells the client that the connection isn't being accepted.

### VAPT relevance

This is one reason TCP scanning can distinguish between different port states.

---

# 15. TCP SYN Scan

Nmap can perform a SYN scan using:

```bash
nmap -sS 192.168.1.20
```

**Only scan systems you own or are explicitly authorized to test.**

A simplified SYN scan:

```text
Scanner → SYN → Target
```

If target responds:

```text
SYN-ACK
```

the scanner can infer that the port is likely open.

The scanner can then send:

```text
RST
```

instead of completing a normal application connection.

Conceptually:

```text
Scanner                     Target

SYN ------------------------>
    <----------------------- SYN-ACK

RST ------------------------>
```

This is different from a full TCP connection.

---

# 16. SYN Scan vs TCP Connect Scan

| Feature                          | SYN Scan   | Connect Scan |
| -------------------------------- | ---------- | ------------ |
| Nmap option                      | `-sS`      | `-sT`        |
| Completes normal TCP connection? | Usually no | Yes          |
| Uses TCP handshake partially?    | Yes        | Yes          |
| Requires raw-packet capability?  | Often      | No           |
| Useful for port discovery        | Yes        | Yes          |

The exact behavior can depend on OS, privileges, networking stack, and filtering.

---

# 17. What Is a Half-Open Connection?

In a SYN scan:

```text
SYN
 ↓
SYN-ACK
 ↓
RST
```

The scanner does not complete the normal connection with the final ACK.

Therefore it is commonly called:

> **Half-open scanning**

---

# 18. What If a Firewall Blocks the Port?

A firewall can affect the observed behavior.

Possible result:

```text
SYN
 ↓
No response
```

or:

```text
SYN
 ↓
ICMP/network error
```

Therefore:

> **No response does not automatically mean "the service is definitely closed."**

It may be filtered.

---

# 19. Open vs Closed vs Filtered

A useful mental model:

```text
OPEN
SYN → SYN-ACK
```

```text
CLOSED
SYN → RST
```

```text
FILTERED
SYN → no useful response
```

But real networks can be more complicated because firewalls, IDS/IPS, routing and rate limiting can change responses.

---

# 20. Wireshark Practical Lab

A very useful beginner lab is observing the handshake yourself.

### Step 1

Open Wireshark.

### Step 2

Start packet capture on your active interface.

### Step 3

Connect to a service you are authorized to test.

For example, a local lab server.

### Step 4

Use this Wireshark filter:

```text
tcp.flags.syn == 1
```

You can also inspect a specific TCP stream using:

```text
tcp.stream
```

---

# 21. What to Look for in Wireshark

Find these three packets:

```text
1. SYN
2. SYN-ACK
3. ACK
```

Inspect:

* Source IP
* Destination IP
* Source port
* Destination port
* Sequence number
* Acknowledgment number
* TCP flags
* Window size
* TCP options

---

# 22. TCP Options

TCP packets may contain options negotiated during connection establishment.

Common examples include:

### MSS

**Maximum Segment Size**

Defines the largest TCP payload segment a host wants to receive.

### Window Scaling

Allows larger effective TCP receive windows.

### SACK

**Selective Acknowledgment**

Allows more precise acknowledgment of received segments.

### Timestamps

Can assist with timing and TCP performance mechanisms.

---

# 23. TCP Window

TCP uses a **receive window** to control how much data can be sent before acknowledgments are required.

Simplified:

```text
Sender
   |
   | Data Data Data
   ↓
Receiver
   |
   | ACK / Window information
   ↓
Sender
```

This contributes to TCP's flow-control mechanism.

---

# 24. Sequence Numbers

TCP uses sequence numbers to keep track of bytes in the stream.

Example:

```text
Segment 1:
Seq = 1000
Data = 500 bytes

Next expected:
1000 + 500 = 1500
```

The receiver can acknowledge received data.

This helps TCP:

* Detect missing data
* Reorder segments
* Track transmitted bytes
* Support reliable delivery

---

# 25. What Happens If a Packet Is Lost?

Suppose:

```text
Segment 1 ✓
Segment 2 ✗
Segment 3 ✓
```

TCP can detect that something is missing through acknowledgments and retransmission mechanisms.

Conceptually:

```text
Sender → Segment 1
Sender → Segment 2  X
Sender → Segment 3

Receiver → ACK information

Sender → retransmit missing data
```

This is one reason TCP is considered reliable.

---

# 26. TCP Connection Termination

The three-way handshake establishes the connection.

Closing a TCP connection normally uses a **FIN-based exchange**, commonly described as a four-segment process.

Simplified:

```text
Client → FIN
Server → ACK
Server → FIN
Client → ACK
```

So remember:

### Establishment

```text
SYN
SYN-ACK
ACK
```

### Typical graceful termination

```text
FIN
ACK
FIN
ACK
```

---

# 27. TCP Reset — RST

`RST` means:

> **Reset the connection**

It can be used when a connection cannot continue normally or a host/service rejects a connection.

Simplified:

```text
Client → connection attempt
Server → RST
```

Possible reasons include:

* No application listening on the port
* Invalid connection state
* Abrupt termination
* Network stack behavior

---

# 28. VAPT Relevance

Understanding the TCP handshake helps you understand:

### Port scanning

```text
SYN → SYN-ACK
```

Can indicate an open TCP port.

### Firewall analysis

```text
SYN → no response
```

May indicate filtering.

### Service enumeration

```text
TCP connection
      ↓
Application protocol
      ↓
Banner / response
```

### Packet analysis

You can recognize:

```text
SYN
SYN-ACK
ACK
RST
FIN
```

### Troubleshooting

You can identify whether a problem occurs during:

```text
DNS
 ↓
TCP
 ↓
TLS
 ↓
HTTP
```

---

# 29. Security Concepts Related to TCP Handshakes

## SYN Flood

A **SYN flood** is a denial-of-service technique that abuses the TCP connection-establishment process by generating many connection attempts.

Conceptually:

```text
Many SYN packets
       ↓
Server allocates connection state
       ↓
Resources become exhausted
       ↓
Legitimate clients may be affected
```

Modern systems use defenses such as:

* SYN cookies
* Connection limits
* Rate limiting
* Firewalls
* Load balancers
* DDoS protection

Don't test this against real systems without explicit authorization.

---

# 30. IDS/IPS and TCP Handshakes

Security monitoring systems can inspect TCP behavior.

For example:

```text
Large SYN spike
      ↓
IDS/IPS
      ↓
Potential SYN-flood pattern
```

They can also detect:

* Port scanning patterns
* Unusual connection rates
* Repeated failed connections
* Suspicious TCP flags
* Abnormal traffic patterns

---

# 31. Common Beginner Mistakes

### ❌ Mistake 1

Thinking:

> SYN = connection established

Wrong.

SYN only starts the connection process.

---

### ❌ Mistake 2

Thinking:

> SYN-ACK means the application is working perfectly

Not necessarily.

It mainly tells you that TCP responded.

---

### ❌ Mistake 3

Confusing TCP handshake with TLS handshake.

They are different.

```text
TCP → connection
TLS → cryptographic security
```

---

### ❌ Mistake 4

Thinking every unanswered SYN means the port is closed.

Wrong.

It may be:

```text
filtered
dropped
unreachable
rate-limited
```

---

### ❌ Mistake 5

Thinking TCP is encrypted.

TCP itself does **not** provide encryption.

For example:

```text
HTTP → TCP → IP
```

HTTP traffic can be unencrypted.

HTTPS adds TLS:

```text
HTTPS → TLS → TCP → IP
```

---

# 32. Beginner → Intermediate → VAPT Progression

## 🟢 Beginner

Understand:

```text
SYN
SYN-ACK
ACK
```

Know:

* TCP is connection-oriented
* Ports identify services/endpoints
* Client initiates
* Server listens

---

## 🟡 Intermediate

Understand:

* Sequence numbers
* ACK numbers
* TCP states
* TCP flags
* Ephemeral ports
* TCP options
* FIN/RST
* Retransmissions

---

## 🔴 VAPT Level

Understand:

* SYN scanning
* Connect scanning
* Open/closed/filtered interpretation
* Firewall effects
* IDS/IPS visibility
* SYN flood concept
* Packet analysis
* Nmap ↔ TCP behavior

---

# 33. Practical Lab

## Lab 1 — Local TCP Server

On your own machine/lab VM:

```bash
python3 -m http.server 8080
```

Then from another authorized machine:

```bash
curl http://<lab-ip>:8080
```

Observe the packets with Wireshark.

Look for:

```text
SYN
SYN-ACK
ACK
HTTP request
HTTP response
FIN/ACK
```

---

# 34. Lab 2 — Nmap + Wireshark

Against your own lab machine:

```bash
nmap -sS -p 8080 <LAB-IP>
```

Capture traffic in Wireshark.

Try to identify:

```text
SYN
SYN-ACK
RST
```

Then compare it with a normal TCP connection.

---

# 35. Lab 3 — Python Socket

Use a local test service and create a simple Python TCP client:

```python
import socket

host = "127.0.0.1"
port = 8080

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.settimeout(3)
    s.connect((host, port))
    print("[+] TCP connection established")
```

Observe the handshake in Wireshark.

This connects your:

```text
Python
   ↓
Socket
   ↓
TCP
   ↓
IP
   ↓
Ethernet/Wi-Fi
```

---

# 36. Interview Questions

### Q1. What is the TCP three-way handshake?

A process used by TCP to establish a connection using SYN, SYN-ACK and ACK.

### Q2. What does SYN mean?

Synchronize.

### Q3. What does ACK mean?

Acknowledgment.

### Q4. Why does TCP use sequence numbers?

To track the byte stream and support reliable, ordered delivery.

### Q5. What happens after SYN-ACK?

The client normally sends the final ACK.

### Q6. What is TCP `RST`?

A reset signal used to terminate/refuse a connection or indicate an invalid connection state.

### Q7. What is a SYN scan?

A scanning technique that sends SYN packets and analyzes TCP responses without normally completing a full TCP connection.

### Q8. What does `nmap -sS` perform?

TCP SYN scanning.

### Q9. Does TCP provide encryption?

No.

### Q10. Is TCP handshake the same as TLS handshake?

No.

---

# 37. Scenario-Based Questions

### Scenario 1

You send:

```text
SYN
```

and receive:

```text
SYN-ACK
```

What can you reasonably conclude?

**Answer:** The TCP port is responding as open/reachable from the scanner's perspective.

---

### Scenario 2

You send:

```text
SYN
```

and receive:

```text
RST
```

What does this commonly indicate?

**Answer:** The destination is reachable but the TCP connection was rejected, commonly because no service is listening on that port.

---

### Scenario 3

You send SYN but receive no response.

Can you immediately say:

> "Port is closed"?

**Answer:** No. Filtering or packet loss may be involved.

---

### Scenario 4

TCP handshake succeeds but HTTP request fails.

Where could the problem be?

```text
TCP ✓
HTTP ✗
```

Possible application-layer issue.

---

# 38. MCQs

### 1. Which packet starts a TCP connection?

A. ACK
B. SYN
C. FIN
D. RST

**Answer: B**

---

### 2. Correct TCP handshake?

A. ACK → SYN → FIN
B. SYN → ACK → FIN
C. SYN → SYN-ACK → ACK
D. FIN → ACK → SYN

**Answer: C**

---

### 3. Which flag is normally used to reset a TCP connection?

A. SYN
B. ACK
C. FIN
D. RST

**Answer: D**

---

### 4. TCP is:

A. Connectionless
B. Connection-oriented
C. Broadcast-only
D. Encryption-only

**Answer: B**

---

### 5. Which Nmap option performs a SYN scan?

A. `-sU`
B. `-sV`
C. `-sS`
D. `-O`

**Answer: C**

---

### 6. TCP itself provides:

A. Encryption
B. Authentication
C. Reliable ordered delivery
D. DNS resolution

**Answer: C**

---

### 7. Which flag is associated with graceful TCP connection termination?

A. FIN
B. SYN
C. PSH
D. URG

**Answer: A**

---

### 8. HTTPS security is primarily provided by:

A. TCP
B. UDP
C. TLS
D. ARP

**Answer: C**

---

### 9. A server normally waits for incoming TCP connections in:

A. SYN-SENT
B. LISTEN
C. TIME-WAIT
D. FIN-WAIT

**Answer: B**

---

### 10. A SYN followed by SYN-ACK commonly indicates:

A. TCP service responded positively
B. DNS failed
C. UDP connection established
D. HTTP encryption completed

**Answer: A**

---

# 39. Must Remember ⭐

```text
TCP = Connection-oriented

3-Way Handshake:

Client                  Server
  |                       |
  | ------ SYN ---------> |
  | <--- SYN + ACK ------ |
  | ------ ACK ---------> |
  |                       |
  |   ESTABLISHED         |
```

### Memorize these:

```text
SYN      → Start/synchronize
SYN-ACK  → Accept + synchronize
ACK      → Confirm
FIN      → Graceful close
RST      → Reset
```

### Nmap

```text
-sS → SYN scan
-sT → TCP connect scan
```

### Important distinction

```text
TCP handshake
     ↓
Network connection

TLS handshake
     ↓
Cryptographic security

HTTP
     ↓
Application communication
```

---

# 40. Final Mental Model

When you access a TCP service, think in layers:

```text
        APPLICATION
       HTTP / HTTPS / SSH
              ↓
             TCP
      SYN → SYN-ACK → ACK
              ↓
              IP
       Source → Destination
              ↓
     Ethernet / Wi-Fi
              ↓
        Physical network
```

And from a VAPT perspective:

```text
Target
  ↓
Is it reachable?
  ↓
Is TCP port responding?
  ↓
What service is running?
  ↓
What version?
  ↓
Is it exposed?
  ↓
Are there security weaknesses?
```

**The key skill is not memorizing "SYN → SYN-ACK → ACK."**
You should be able to look at a Wireshark capture or Nmap result and explain **what happened, why it happened, and what it means for the network/security assessment.**


