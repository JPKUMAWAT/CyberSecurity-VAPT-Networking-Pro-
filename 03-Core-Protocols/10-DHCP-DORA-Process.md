

# 1. What Is DHCP?

**DHCP = Dynamic Host Configuration Protocol**

DHCP automatically provides network configuration to a device so it can communicate on a network.

Instead of manually configuring:

* IP address
* Subnet mask
* Default gateway
* DNS server

a device can request this information from a **DHCP server**.

### Simple Example

When your laptop connects to Wi-Fi:

```text
Laptop
   |
   | "I need network configuration"
   ↓
DHCP Server
   |
   | "Here is your IP + network information"
   ↓
Laptop
```

For example, the laptop may receive:

```text
IP Address       → 192.168.1.25
Subnet Mask      → 255.255.255.0
Default Gateway  → 192.168.1.1
DNS Server       → 192.168.1.1
Lease Time       → 8 hours
```

---

# 2. Why DHCP Is Important

Without DHCP, administrators would have to manually configure every device.

Imagine a network with:

```text
500 computers
100 phones
50 printers
20 servers
```

Manually configuring each device would be inefficient and error-prone.

DHCP automates this process.

---

# 3. DHCP Works at Which Layer?

DHCP operates at the **Application Layer** of the TCP/IP model.

It uses:

```text
DHCP
 ↓
UDP
 ↓
IP
 ↓
Ethernet/Wi-Fi
```

DHCP normally uses:

| Direction       |   Port |
| --------------- | -----: |
| Client → Server | UDP 67 |
| Server → Client | UDP 68 |

### Must Remember

```text
DHCP Server = UDP 67
DHCP Client = UDP 68
```

---

# 4. DHCP Uses UDP

DHCP uses **UDP**, not TCP.

Why?

At the beginning of the process, the client may not yet have:

* an IP address
* a default gateway
* a known DHCP server

Therefore, a lightweight connectionless protocol is useful.

```text
DHCP
  ↓
UDP
  ↓
IP
```

---

# 5. The DORA Process

The most important DHCP concept is:

# DORA

```text
D → Discover
O → Offer
R → Request
A → Acknowledgment
```

The process looks like:

```text
Client                         DHCP Server
  |                                  |
  |------ DHCP DISCOVER ------------>|
  |                                  |
  |<------- DHCP OFFER --------------|
  |                                  |
  |------ DHCP REQUEST ------------->|
  |                                  |
  |<------ DHCP ACK -----------------|
  |                                  |
  |       Configuration received     |
```

Let's understand each step.

---

# 6. Step 1 — DHCP Discover

The client doesn't know where the DHCP server is.

So it broadcasts a **DHCP Discover** message.

Conceptually:

```text
Client:

"I need an IP address.
Is there any DHCP server available?"
```

The client may initially use:

```text
Source IP:
0.0.0.0

Destination IP:
255.255.255.255
```

Because it doesn't yet have a usable IP configuration.

---

# 7. Why Broadcast?

The client may not know the DHCP server's IP address.

Therefore it broadcasts the request within the local network.

```text
Laptop
  |
  | Broadcast
  ↓
Switch
  |
  ├── DHCP Server
  ├── PC
  ├── Printer
  └── Phone
```

The DHCP server can receive the broadcast and respond.

---

# 8. Step 2 — DHCP Offer

The DHCP server receives the Discover message.

It can respond with an **Offer**.

Example:

```text
DHCP Server:

"I can give you:

IP:       192.168.1.25
Mask:     255.255.255.0
Gateway:  192.168.1.1
DNS:      192.168.1.1
Lease:    8 hours
"
```

This is called a **DHCP Offer**.

Important:

> An Offer is a proposed configuration, not necessarily the final confirmation.

---

# 9. Step 3 — DHCP Request

The client chooses an offer and sends a **DHCP Request**.

Conceptually:

```text
Client:

"I want the configuration offered by this DHCP server."
```

This also lets other DHCP servers know that their offers weren't selected.

---

# 10. Step 4 — DHCP ACK

The DHCP server sends:

**DHCP ACK = DHCP Acknowledgment**

This confirms the configuration.

Example:

```text
DHCP ACK

IP Address:
192.168.1.25

Subnet:
255.255.255.0

Gateway:
192.168.1.1

DNS:
192.168.1.1
```

The client can now configure its network interface.

---

# 11. Complete DORA Example

Imagine your laptop joins:

```text
Wi-Fi Network
192.168.1.0/24
```

### Step 1

```text
Laptop → DHCP Discover
```

> "Is there a DHCP server?"

### Step 2

```text
DHCP Server → DHCP Offer
```

> "Use 192.168.1.25."

### Step 3

```text
Laptop → DHCP Request
```

> "I'll take 192.168.1.25."

### Step 4

```text
DHCP Server → DHCP ACK
```

> "Confirmed."

Final configuration:

```text
IP       = 192.168.1.25
Mask     = 255.255.255.0
Gateway  = 192.168.1.1
DNS      = 192.168.1.1
```

---

# 12. What Information Can DHCP Provide?

DHCP can provide much more than just an IP address.

Common options include:

| DHCP Option | Purpose                |
| ----------- | ---------------------- |
| Option 1    | Subnet Mask            |
| Option 3    | Default Gateway        |
| Option 6    | DNS Servers            |
| Option 15   | DNS Domain Name        |
| Option 51   | IP Address Lease Time  |
| Option 54   | DHCP Server Identifier |

This is important because DHCP is effectively distributing network configuration to clients.

---

# 13. DHCP Lease

DHCP addresses are often **leased**, rather than permanently assigned.

Example:

```text
IP: 192.168.1.25
Lease: 8 hours
```

The client can normally renew the lease before it expires.

This allows DHCP servers to reuse addresses.

---

# 14. DHCP Renewal

The DHCP process doesn't necessarily happen only once.

A client can attempt to renew its lease.

Simplified:

```text
Client
  |
  | "Can I continue using 192.168.1.25?"
  ↓
DHCP Server
  |
  | "Yes"
  ↓
Client
```

If the original DHCP server is unavailable, the client can use later renewal/rebinding stages to try to maintain or obtain configuration.

---

# 15. DHCP vs Static IP

| DHCP                    | Static IP                          |
| ----------------------- | ---------------------------------- |
| Automatically assigned  | Manually configured                |
| Usually leased          | Usually persistent                 |
| Easy for large networks | Useful for infrastructure          |
| Less manual work        | More administrative work           |
| Common for clients      | Common for servers/network devices |

### Example

Normal laptop:

```text
DHCP
```

Network printer/server:

```text
Static or DHCP reservation
```

---

# 16. DHCP Reservation

A DHCP server can reserve a particular IP for a specific device, commonly based on its MAC address.

Example:

```text
MAC:
AA:BB:CC:11:22:33

↓ DHCP reservation

IP:
192.168.1.50
```

Whenever that device requests an address, the DHCP server can provide the reserved address.

This gives some of the convenience of DHCP while keeping predictable addressing.

---

# 17. DHCP Relay

A common misconception is:

> "The DHCP server must be on the same subnet."

Not necessarily.

Routers normally don't forward broadcasts between subnets.

A **DHCP relay agent** can forward DHCP messages between the client network and a DHCP server located elsewhere.

```text
Client
  |
  | DHCP Broadcast
  ↓
Router / L3 Switch
  |
  | DHCP Relay
  ↓
DHCP Server
```

This is very common in enterprise networks.

---

# 18. DHCP and Routers

A home router often performs several functions simultaneously:

```text
                 Home Router
        ┌──────────┼──────────┐
        ↓          ↓          ↓
      DHCP        DNS        NAT
        ↓
   IP assignment
```

Don't confuse these functions:

### DHCP

Provides network configuration.

### DNS

Converts names to IP addresses.

### NAT

Translates addresses between network boundaries.

---

# 19. DHCP vs DNS

Very important distinction.

### DHCP

```text
"What IP configuration should this device use?"
```

### DNS

```text
"What IP address belongs to example.com?"
```

Example:

```text
Laptop
  |
  | DHCP
  ↓
192.168.1.25
```

Then:

```text
Laptop
  |
  | DNS query:
  | "What is example.com?"
  ↓
DNS Server
  |
  ↓
IP address
```

---

# 20. DHCP vs ARP

These are also different.

### DHCP

Helps a device obtain network configuration.

### ARP

On IPv4 networks, helps discover the MAC address associated with a local IPv4 address.

Example:

```text
DHCP:
"I received 192.168.1.25."

ARP:
"Who has 192.168.1.1?"
```

---

# 21. DHCP and MAC Address

Before receiving a normal IP configuration, the DHCP server can identify a client using information such as its **MAC address** at the local network level.

Simplified:

```text
Device
  ↓
Network Interface
  ↓
MAC address
  ↓
DHCP communication
```

However, don't think of MAC address as a permanent identity for every DHCP/security scenario—modern operating systems can use **MAC address randomization**, especially on Wi-Fi.

---

# 22. DHCP in VAPT

DHCP itself can become part of the security assessment because incorrect DHCP configuration can affect the network's trust model.

A pentester may investigate:

* Who is allowed to provide DHCP?
* Are unauthorized DHCP servers possible?
* Are DHCP messages properly controlled?
* Are network segments isolated?
* Are access ports protected?
* Are DHCP logs monitored?
* Are rogue-device protections configured?

---

# 23. Rogue DHCP Server

A **rogue DHCP server** is an unauthorized DHCP server operating on a network.

Example:

```text
Legitimate DHCP
      |
      | Gives correct configuration
      ↓

Client

      ↑
      |
Rogue DHCP
      |
      | Gives malicious/incorrect configuration
```

If clients accept the rogue server's configuration, an attacker could potentially influence network settings.

Depending on the network architecture and configuration, this could affect:

* Default gateway
* DNS server
* Routing behavior
* Network access

This is why unauthorized DHCP servers are a security concern.

> Only test this in an authorized lab or assessment.

---

# 24. DHCP Starvation

Another DHCP-related attack concept is **DHCP starvation**.

The basic idea is to consume available DHCP leases by generating many client requests.

Conceptually:

```text
DHCP Pool

192.168.1.10
192.168.1.11
192.168.1.12
...
192.168.1.254

        ↑
        |
Many fake/unauthorized requests
```

If the address pool becomes exhausted, legitimate clients may have difficulty obtaining an IP configuration.

### Defensive Controls

Enterprise networks can use protections such as:

* DHCP snooping
* Port security
* Rate limiting
* Network segmentation
* Monitoring

---

# 25. DHCP Snooping

**DHCP Snooping** is a switch security feature designed to distinguish trusted and untrusted DHCP sources.

Conceptually:

```text
                 Switch
              /          \
             /            \
     Trusted port       Untrusted port
          |                   |
   Legit DHCP Server      Client/device
```

The switch can allow DHCP server responses only from trusted interfaces.

This helps mitigate certain rogue DHCP server attacks.

---

# 26. DHCP + VLANs

Enterprise networks often use VLANs.

Example:

```text
VLAN 10 → Employees
VLAN 20 → Guests
VLAN 30 → Servers
```

Each network can have different DHCP configuration.

```text
VLAN 10
   ↓
DHCP Scope A

VLAN 20
   ↓
DHCP Scope B

VLAN 30
   ↓
DHCP Scope C
```

This is important for **network segmentation**.

---

# 27. DHCP Scope

A **DHCP scope** is a defined range of addresses that a DHCP server can allocate.

Example:

```text
Network:
192.168.1.0/24

DHCP Pool:
192.168.1.100
      ↓
192.168.1.200
```

Possible exclusions:

```text
192.168.1.1 → Router
192.168.1.10 → Server
192.168.1.20 → Printer
```

---

# 28. Practical Linux Commands

### Check IP configuration

```bash
ip addr
```

or:

```bash
ip a
```

### Check routing

```bash
ip route
```

### Check DNS configuration

Depending on the Linux distribution/configuration:

```bash
cat /etc/resolv.conf
```

### Renew DHCP lease

On systems using NetworkManager, one approach is:

```bash
sudo nmcli device show
```

The exact DHCP renewal command depends on the network-management system being used, so don't blindly apply commands from another Linux distribution.

---

# 29. Windows DHCP-Related Commands

### View IP configuration

```cmd
ipconfig /all
```

This is especially useful because it shows information such as:

```text
IPv4 Address
Subnet Mask
Default Gateway
DHCP Server
DNS Servers
```

### Release DHCP lease

```cmd
ipconfig /release
```

### Request a new lease

```cmd
ipconfig /renew
```

These commands are useful for troubleshooting your own/authorized systems.

---

# 30. Troubleshooting DHCP

Suppose your computer says:

```text
169.254.x.x
```

This is an **IPv4 link-local address** (APIPA) commonly assigned by Windows when DHCP configuration isn't successfully obtained.

Possible causes include:

* DHCP server unavailable
* Network cable/Wi-Fi issue
* VLAN configuration problem
* DHCP relay problem
* Switch configuration issue
* Address pool exhaustion
* Firewall/network policy issue

A useful troubleshooting flow:

```text
Physical connection
       ↓
Interface status
       ↓
DHCP availability
       ↓
IP configuration
       ↓
Default gateway
       ↓
DNS
       ↓
Internet/application connectivity
```

---

# 31. DHCP Packet-Level Mental Model

At a simplified level:

```text
Client
  |
  | DHCP Discover
  | UDP
  | Client 68 → Server 67
  ↓
Network
  |
  ↓
DHCP Server
  |
  | DHCP Offer
  ↓
Client
  |
  | DHCP Request
  ↓
DHCP Server
  |
  | DHCP ACK
  ↓
Client configured
```

Remember:

```text
D → Discover
O → Offer
R → Request
A → ACK
```

---

# 32. Common Beginner Mistakes

### ❌ Mistake 1

Thinking DHCP gives only an IP address.

### ✅ Correct

DHCP can provide multiple configuration parameters.

---

### ❌ Mistake 2

Thinking DHCP uses TCP.

### ✅ Correct

DHCP normally uses UDP.

```text
Server → 67
Client → 68
```

---

### ❌ Mistake 3

Confusing DHCP with DNS.

### ✅ Correct

```text
DHCP → network configuration
DNS  → name resolution
```

---

### ❌ Mistake 4

Thinking DHCP is the same as NAT.

### ✅ Correct

```text
DHCP → assigns/configures addresses
NAT  → translates addresses
```

---

### ❌ Mistake 5

Thinking DHCP server must always be physically on the same subnet.

### ✅ Correct

DHCP relay can forward DHCP traffic to a server on another network.

---

# 33. Interview Questions

### Q1. What does DHCP stand for?

**Dynamic Host Configuration Protocol**

### Q2. What does DORA mean?

```text
Discover
Offer
Request
Acknowledgment
```

### Q3. Which transport protocol does DHCP use?

**UDP**

### Q4. What are DHCP ports?

```text
Server → UDP 67
Client → UDP 68
```

### Q5. Why is DHCP useful?

It automatically provides network configuration to clients.

### Q6. What is DHCP lease?

A temporary allocation of an IP configuration for a defined period.

### Q7. What is DHCP relay?

A mechanism that forwards DHCP messages between clients and a DHCP server across network boundaries.

### Q8. What is rogue DHCP?

An unauthorized DHCP server providing potentially incorrect or malicious configuration.

### Q9. What is DHCP snooping?

A switch security feature that helps control which interfaces are trusted to send DHCP server responses.

### Q10. What is DHCP starvation?

An attack that attempts to exhaust available DHCP address leases.

---

# 34. Scenario-Based Questions

### Scenario 1

Your laptop receives:

```text
169.254.20.10
```

What might this indicate?

**Answer:** DHCP configuration likely failed, so Windows assigned an IPv4 link-local address.

---

### Scenario 2

A company has 20 VLANs but only one centralized DHCP server.

How can clients in different VLANs receive addresses?

**Answer:** DHCP relay can forward requests to the centralized server.

---

### Scenario 3

Users suddenly receive an unexpected DNS server through DHCP.

What should a security team investigate?

**Answer:** Possible unauthorized/rogue DHCP activity and switch/network controls.

---

### Scenario 4

The DHCP pool is completely exhausted.

What can happen?

**Answer:** New legitimate clients may fail to obtain an IP address.

---

# 35. MCQs

### 1. DHCP stands for:

A. Dynamic Host Configuration Protocol
B. Dynamic HTTP Control Protocol
C. Domain Host Configuration Protocol
D. Distributed Host Communication Protocol

**Answer: A**

---

### 2. Which port is normally used by a DHCP server?

A. 53
B. 67
C. 68
D. 80

**Answer: B**

---

### 3. Which port is normally used by a DHCP client?

A. 22
B. 53
C. 67
D. 68

**Answer: D**

---

### 4. What does DORA represent?

A. DNS process
B. DHCP process
C. TCP handshake
D. ARP process

**Answer: B**

---

### 5. The first DORA step is:

A. Request
B. ACK
C. Discover
D. Offer

**Answer: C**

---

### 6. DHCP normally uses:

A. TCP
B. UDP
C. ICMP
D. ARP

**Answer: B**

---

### 7. Which service resolves domain names?

A. DHCP
B. DNS
C. NAT
D. ARP

**Answer: B**

---

### 8. What is a rogue DHCP server?

A. Backup DNS server
B. Unauthorized DHCP server
C. Firewall
D. NAT router

**Answer: B**

---

### 9. What helps protect against rogue DHCP servers?

A. DHCP snooping
B. DNS caching
C. HTTP
D. FTP

**Answer: A**

---

### 10. What does DHCP ACK indicate?

A. DHCP server is unavailable
B. Configuration is acknowledged
C. DNS lookup failed
D. TCP connection closed

**Answer: B**

---

# 36. Practical Lab — Observe Your Own DHCP Configuration

## Windows

Run:

```cmd
ipconfig /all
```

Look for:

```text
IPv4 Address
Subnet Mask
Default Gateway
DHCP Server
DNS Servers
```

Draw the relationship:

```text
Your PC
  |
  | DHCP
  ↓
DHCP Server
  |
  ├── IP
  ├── Subnet Mask
  ├── Gateway
  └── DNS
```

---

## Kali/Linux

Run:

```bash
ip addr
```

Then:

```bash
ip route
```

And inspect DNS configuration:

```bash
cat /etc/resolv.conf
```

Try to identify:

```text
Your IP
Network
Default Gateway
DNS
```

This is a safe local learning exercise.

---

# 37. VAPT Workflow Connection

DHCP is not usually the first thing you exploit during a normal web VAPT.

But it becomes important during **internal network assessments**.

A simplified internal assessment might look like:

```text
Network Access
      ↓
DHCP / IP Configuration
      ↓
Identify Network
      ↓
Identify Gateway
      ↓
Discover Hosts
      ↓
Enumerate Services
      ↓
Analyze Segmentation
      ↓
Assess Security Controls
```

DHCP information helps you understand the network environment.

---

# 38. Advanced Concepts to Learn Later

After mastering basic DHCP, explore:

* DHCP relay
* DHCP snooping
* DHCP starvation
* Rogue DHCP detection
* DHCP option security
* VLAN security
* 802.1X
* Network Access Control (NAC)
* IPv6 DHCPv6
* Router Advertisement security
* Network segmentation
* Switch security

Don't try to memorize all of these initially. Understand the **DORA process first**.

---

# 39. Must-Memorize Cheat Sheet

```text
DHCP
│
├── Full Form
│   └── Dynamic Host Configuration Protocol
│
├── Layer
│   └── Application Layer
│
├── Transport
│   └── UDP
│
├── Server Port
│   └── 67
│
├── Client Port
│   └── 68
│
├── DORA
│   ├── Discover
│   ├── Offer
│   ├── Request
│   └── ACK
│
├── Provides
│   ├── IP Address
│   ├── Subnet Mask
│   ├── Gateway
│   ├── DNS
│   └── Lease information
│
└── Security
    ├── Rogue DHCP
    ├── DHCP Starvation
    └── DHCP Snooping
```

---

# 40. Final Mental Model

Don't memorize DHCP as four random words.

Think about the story:

```text
                 DHCP

Laptop joins network
        ↓
"I need configuration"
        ↓
DISCOVER
        ↓
"Here is an available configuration"
        ↓
OFFER
        ↓
"I choose this configuration"
        ↓
REQUEST
        ↓
"Confirmed"
        ↓
ACK
        ↓
Laptop configures itself
        ↓
Network communication begins
```

### One-line memory trick:

> **DORA = Discover → Offer → Request → Acknowledge**

And remember the most important numbers:

> **DHCP Server = UDP 67 | DHCP Client = UDP 68**

---

# Key Takeaways

* DHCP automatically provides network configuration.
* DHCP normally uses UDP.
* Server listens on **67**, client uses **68**.
* DORA describes the basic DHCP address-assignment sequence.
* DHCP can provide IP, subnet mask, gateway, DNS and other options.
* DHCP addresses are commonly leased.
* DHCP relay allows centralized DHCP across network boundaries.
* Rogue DHCP and DHCP starvation are important security concepts.
* DHCP snooping helps defend against unauthorized DHCP servers.
* DHCP, DNS, ARP and NAT perform **different jobs**.

