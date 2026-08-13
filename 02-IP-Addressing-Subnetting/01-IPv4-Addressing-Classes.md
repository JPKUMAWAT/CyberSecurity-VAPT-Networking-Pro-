

## 1. What is an IP Address?

An **IP (Internet Protocol) address** is a logical address assigned to a network interface so devices can communicate over an IP network.

Think of it like a **postal address for a device/interface**.

Example:

```text
192.168.1.10
```

It helps the network determine:

```text
Who am I?
Where is the destination?
Where should the packet go?
```

### Important

An IP address identifies a **network interface**, not necessarily a physical computer.

A single device can have multiple interfaces and therefore multiple IP addresses.

---

# 2. What is IPv4?

**IPv4 = Internet Protocol version 4**

IPv4 uses **32 bits**.

These 32 bits are divided into **4 octets**, with each octet containing 8 bits.

```text
32 bits
│
├──── 8 ────┤├──── 8 ────┤├──── 8 ────┤├──── 8 ────┤
   Octet 1      Octet 2      Octet 3      Octet 4

     192           168           1            10
```

Therefore:

```text
192.168.1.10
```

contains:

```text
4 × 8 = 32 bits
```

---

# 3. IPv4 Range

Each octet contains 8 bits.

An 8-bit value can represent:

```text
0 → 255
```

Therefore an IPv4 address has the theoretical range:

```text
0.0.0.0 → 255.255.255.255
```

But **not every address is usable as a normal host address**.

Some ranges are reserved for special purposes.

---

# 4. Binary Representation

Understanding binary is important for subnetting.

Example:

```text
192
```

Binary:

```text
11000000
```

The positional values are:

```text
128 64 32 16 8 4 2 1
```

For 192:

```text
128 + 64 = 192

11000000
```

Example:

```text
168 = 10101000
```

Therefore:

```text
192.168.1.10
```

becomes:

```text
11000000.10101000.00000001.00001010
```

---

# 5. Network Portion vs Host Portion

An IPv4 address generally contains:

```text
Network portion + Host portion
```

Example:

```text
192.168.1.10/24
```

With `/24`:

```text
Network bits = 24
Host bits    = 8
```

Conceptually:

```text
192.168.1 | 10
-----------|---
 Network  |Host
```

The subnet mask tells us where the network portion ends.

```text
255.255.255.0
```

---

# 6. What is a Subnet Mask?

A **subnet mask** determines which bits belong to the network portion and which belong to the host portion.

Example:

```text
IP:          192.168.1.10
Subnet mask: 255.255.255.0
```

Binary mask:

```text
11111111.11111111.11111111.00000000
```

Therefore:

```text
24 network bits
8 host bits
```

This is written as:

```text
192.168.1.10/24
```

`/24` is called **CIDR notation**.

---

# 7. IPv4 Address Classes

Traditional IPv4 classful addressing divided addresses into:

```text
Class A
Class B
Class C
Class D
Class E
```

> ⚠️ **Important:** Modern networks primarily use **CIDR**, not traditional classful addressing. Classes are still important for exams, historical understanding, and basic networking concepts.

---

# 8. Class A

### Range

```text
1.0.0.0 → 126.255.255.255
```

Default subnet mask:

```text
255.0.0.0
```

CIDR:

```text
/8
```

Structure:

```text
Network.Host.Host.Host
```

Example:

```text
10.20.30.40
```

Historically:

```text
Network = 10
Host    = 20.30.40
```

### Characteristics

```text
Network bits = 8
Host bits    = 24
```

Class A was designed for very large networks.

### Important

`127.0.0.0/8` is reserved for **loopback**, so it isn't treated as ordinary Class A host space.

---

# 9. Class B

### Range

```text
128.0.0.0 → 191.255.255.255
```

Default subnet mask:

```text
255.255.0.0
```

CIDR:

```text
/16
```

Structure:

```text
Network.Network.Host.Host
```

Example:

```text
172.16.20.50
```

Historically:

```text
Network = 172.16
Host    = 20.50
```

Characteristics:

```text
Network bits = 16
Host bits    = 16
```

---

# 10. Class C

### Range

```text
192.0.0.0 → 223.255.255.255
```

Default subnet mask:

```text
255.255.255.0
```

CIDR:

```text
/24
```

Structure:

```text
Network.Network.Network.Host
```

Example:

```text
192.168.1.25
```

Historically:

```text
Network = 192.168.1
Host    = 25
```

Characteristics:

```text
Network bits = 24
Host bits    = 8
```

---

# 11. Class D

Class D is used for **multicast**.

Range:

```text
224.0.0.0 → 239.255.255.255
```

It is **not normal unicast host addressing**.

Example multicast address:

```text
224.0.0.1
```

represents the multicast group for all IPv4 hosts on the local subnet.

### VAPT relevance

During network enumeration, multicast traffic can reveal information about services and network architecture.

---

# 12. Class E

Class E:

```text
240.0.0.0 → 255.255.255.255
```

Historically reserved for experimental/future use.

It is not ordinary public host addressing.

---

# 13. Class Table — Must Know

| Class | First Octet | Traditional Range         | Default Mask | Purpose               |
| ----- | ----------: | ------------------------- | ------------ | --------------------- |
| A     |       1–126 | 1.0.0.0–126.255.255.255   | /8           | Large networks        |
| B     |     128–191 | 128.0.0.0–191.255.255.255 | /16          | Medium networks       |
| C     |     192–223 | 192.0.0.0–223.255.255.255 | /24          | Smaller networks      |
| D     |     224–239 | 224.0.0.0–239.255.255.255 | N/A          | Multicast             |
| E     |     240–255 | 240.0.0.0–255.255.255.255 | N/A          | Reserved/experimental |

### Memorize:

```text
A → 1–126
B → 128–191
C → 192–223
D → 224–239
E → 240–255
```

---

# 14. Why is 127 Missing?

You may notice:

```text
Class A → 1–126
Class B → 128–191
```

What happened to:

```text
127
```

`127.0.0.0/8` is reserved for **loopback**.

Most commonly:

```text
127.0.0.1
```

is called:

```text
localhost
```

It refers back to the local host.

---

# 15. Private IPv4 Address Ranges

Private IP addresses are intended for use inside private networks.

There are **three main RFC 1918 private ranges**:

### Class A private range

```text
10.0.0.0/8
```

Range:

```text
10.0.0.0 → 10.255.255.255
```

### Class B private range

```text
172.16.0.0/12
```

Range:

```text
172.16.0.0 → 172.31.255.255
```

### Class C private range

```text
192.168.0.0/16
```

Range:

```text
192.168.0.0 → 192.168.255.255
```

### Easy memory trick

```text
10.x.x.x

172.16.x.x → 172.31.x.x

192.168.x.x
```

---

# 16. Public vs Private IP

### Private

Used inside internal networks.

Example:

```text
192.168.1.10
```

### Public

Globally routable address space assigned for Internet use, subject to the relevant allocation and routing policies.

Example:

```text
203.0.113.10
```

> `203.0.113.0/24` is actually a **documentation range**, so don't use it as an example of a real Internet-routable public host.

---

# 17. NAT

Private IP addresses commonly access the Internet through **NAT (Network Address Translation)**.

Example:

```text
Laptop
192.168.1.10
      |
      ↓
Router/NAT
      |
      ↓
Public IP
      |
      ↓
Internet
```

NAT translates addresses between network contexts.

### VAPT relevance

Understanding NAT helps you determine:

* Which systems are directly exposed
* Which systems are behind a gateway
* Whether an observed public IP represents a router, firewall, load balancer, or host
* How internal and external attack surfaces differ

---

# 18. Special IPv4 Addresses

## 18.1 Loopback

```text
127.0.0.0/8
```

Common:

```text
127.0.0.1
```

Purpose:

```text
Local machine communication
```

---

## 18.2 Unspecified Address

```text
0.0.0.0
```

Meaning depends on context.

For example, a server listening on:

```text
0.0.0.0:8080
```

usually means it is listening on **all available IPv4 interfaces**.

It does **not** mean "the Internet."

---

## 18.3 Limited Broadcast

```text
255.255.255.255
```

Used for local broadcast purposes.

---

## 18.4 Link-local / APIPA

```text
169.254.0.0/16
```

Often automatically assigned when a host cannot obtain an IPv4 address through normal configuration such as DHCP.

Example:

```text
169.254.20.5
```

---

# 19. Network Address and Broadcast Address

Consider:

```text
192.168.1.10/24
```

Network:

```text
192.168.1.0
```

Broadcast:

```text
192.168.1.255
```

Typical usable host range:

```text
192.168.1.1
       ↓
192.168.1.254
```

So:

```text
Network   → .0
Hosts     → .1 – .254
Broadcast → .255
```

For a traditional `/24` subnet.

---

# 20. How a Router Uses IP Addresses

Suppose:

```text
PC A
192.168.1.10/24
```

wants to communicate with:

```text
PC B
192.168.1.20/24
```

Both belong to:

```text
192.168.1.0/24
```

So they are on the same IP subnet.

But if:

```text
PC A = 192.168.1.10/24
Server = 192.168.2.10/24
```

they are on different subnets.

Traffic normally needs a router/default gateway:

```text
PC
 |
 | 192.168.1.10
 ↓
Default Gateway
192.168.1.1
 |
 ↓
Router
 |
 ↓
192.168.2.10
```

---

# 21. IPv4 Addressing in VAPT

IP addressing is extremely important during reconnaissance.

A pentester may need to determine:

```text
Target
 ↓
IP address
 ↓
Network/subnet
 ↓
Reachable hosts
 ↓
Open ports
 ↓
Services
 ↓
Versions
 ↓
Potential vulnerabilities
```

Example:

```text
192.168.1.0/24
```

Potential hosts:

```text
192.168.1.1
192.168.1.2
192.168.1.3
...
192.168.1.254
```

This gives an idea of the possible IPv4 address space within that subnet.

**Only perform scanning against systems you own or are explicitly authorized to test.**

---

# 22. Useful Linux Commands

### Show IPv4 addresses

```bash
ip -4 addr
```

### Show routing table

```bash
ip route
```

### Show a particular interface

```bash
ip addr show eth0
```

### Test your local loopback

```bash
ping -c 4 127.0.0.1
```

---

# 23. Windows Commands

### Display network configuration

```cmd
ipconfig
```

Detailed information:

```cmd
ipconfig /all
```

### Test connectivity

```cmd
ping 127.0.0.1
```

### Display routing table

```cmd
route print
```

---

# 24. Example: Reading Your Network Configuration

Suppose:

```text
IPv4 Address : 192.168.1.25
Subnet Mask  : 255.255.255.0
Gateway      : 192.168.1.1
```

We can understand:

```text
Network:
192.168.1.0/24

Host:
192.168.1.25

Likely gateway:
192.168.1.1

Broadcast:
192.168.1.255
```

This is basic network enumeration knowledge.

---

# 25. Common Beginner Mistakes

### ❌ Mistake 1

Thinking every `192.x.x.x` address is private.

Correct:

```text
192.168.0.0/16
```

is the RFC 1918 private range.

---

### ❌ Mistake 2

Thinking `172.x.x.x` is always private.

Wrong.

Only:

```text
172.16.0.0/12
```

is private.

Therefore:

```text
172.16.10.5 → Private
172.31.20.5 → Private
172.32.20.5 → NOT RFC1918 private
```

---

### ❌ Mistake 3

Thinking `127.0.0.1` is your LAN IP.

Wrong.

It is loopback.

---

### ❌ Mistake 4

Thinking IP address alone identifies a specific application.

An IP identifies an interface/address; ports identify transport endpoints, and services operate behind those endpoints.

Example:

```text
192.168.1.10:22 → SSH
192.168.1.10:80 → HTTP
192.168.1.10:443 → HTTPS
```

---

### ❌ Mistake 5

Thinking Class A/B/C is how modern subnetting works.

Modern networks use:

```text
CIDR
```

Example:

```text
192.168.1.0/27
```

not simply the old Class C `/24`.

---

# 26. Advanced Concept: CIDR

CIDR stands for:

**Classless Inter-Domain Routing**

Example:

```text
192.168.1.0/24
```

`/24` means:

```text
24 network bits
8 host bits
```

Another example:

```text
192.168.1.0/26
```

means:

```text
26 network bits
6 host bits
```

CIDR is the foundation for modern subnetting and route aggregation.

You will study this deeply in:

```text
02-IP-Addressing-Subnetting/
02-CIDR-Notation-Deep-Dive.md
03-Subnetting-Step-by-Step.md
```

---

# 27. VAPT Mental Model

When you see:

```text
10.10.20.15
```

don't just memorize the number.

Ask:

```text
What network is this in?
        ↓
What subnet mask/CIDR applies?
        ↓
Is it private or public?
        ↓
What is the default gateway?
        ↓
What other authorized hosts may exist?
        ↓
What services are exposed?
        ↓
What security controls exist?
```

This is the transition from **memorizing networking** to **thinking like a security tester**.

---

# 28. Interview Questions

### Q1. How many bits are in IPv4?

**Answer:** 32 bits.

---

### Q2. How many octets are in IPv4?

**Answer:** 4.

---

### Q3. What is the size of each octet?

**Answer:** 8 bits.

---

### Q4. What is the range of one IPv4 octet?

**Answer:**

```text
0–255
```

---

### Q5. What is the default mask of traditional Class A?

**Answer:**

```text
255.0.0.0 (/8)
```

---

### Q6. What is the default mask of traditional Class B?

**Answer:**

```text
255.255.0.0 (/16)
```

---

### Q7. What is the default mask of traditional Class C?

**Answer:**

```text
255.255.255.0 (/24)
```

---

### Q8. What is `127.0.0.1`?

**Answer:** Loopback address.

---

### Q9. Is `172.20.10.5` private?

**Answer:** Yes, because it falls inside `172.16.0.0/12`.

---

### Q10. Is `172.50.10.5` RFC1918 private?

**Answer:** No.

---

### Q11. What is `10.0.0.0/8`?

**Answer:** RFC 1918 private IPv4 range.

---

### Q12. What is CIDR?

**Answer:** A classless method of representing IP network prefixes.

---

# 29. Scenario Questions

### Scenario 1

You find:

```text
192.168.10.50
```

Is it likely a private IPv4 address?

**Yes.**

Why?

```text
192.168.0.0/16
```

is RFC 1918 private space.

---

### Scenario 2

You find:

```text
172.15.10.10
```

Is it RFC1918 private?

**No.**

The private range starts at:

```text
172.16.0.0
```

---

### Scenario 3

A service listens on:

```text
0.0.0.0:8080
```

What does that commonly indicate?

The service is listening on all available IPv4 interfaces.

---

# 30. MCQs

### 1. IPv4 uses how many bits?

A. 16
B. 32
C. 64
D. 128

**Answer: B**

---

### 2. An IPv4 address contains how many octets?

A. 2
B. 4
C. 6
D. 8

**Answer: B**

---

### 3. Which is RFC1918 private?

A. 8.8.8.8
B. 10.10.10.10
C. 1.1.1.1
D. 172.50.1.1

**Answer: B**

---

### 4. Which range is private?

A. `172.16.0.0/12`
B. `172.0.0.0/12`
C. `173.16.0.0/12`
D. `171.16.0.0/12`

**Answer: A**

---

### 5. `127.0.0.1` is:

A. Broadcast
B. Multicast
C. Loopback
D. Public

**Answer: C**

---

### 6. Class D is associated with:

A. Unicast
B. Multicast
C. Loopback
D. Private addressing

**Answer: B**

---

### 7. Traditional Class C uses:

A. `/8`
B. `/16`
C. `/24`
D. `/32`

**Answer: C**

---

### 8. Which command displays IPv4 information on Linux?

A. `ip -4 addr`
B. `show-ip`
C. `net-ip`
D. `ipv4-show`

**Answer: A**

---

### 9. Which is the private `192.168` range?

A. `192.168.0.0/8`
B. `192.168.0.0/16`
C. `192.0.0.0/16`
D. `192.168.0.0/24`

**Answer: B**

---

### 10. Modern IP networks primarily use:

A. Class A only
B. Class B only
C. Class C only
D. CIDR

**Answer: D**

---

# 31. Must Remember 🔥

```text
IPv4 = 32 bits
4 octets
1 octet = 8 bits
Octet range = 0–255
```

### Traditional classes

```text
A → 1–126 → /8
B → 128–191 → /16
C → 192–223 → /24
D → 224–239 → Multicast
E → 240–255 → Reserved
```

### Important special ranges

```text
10.0.0.0/8          → Private
172.16.0.0/12       → Private
192.168.0.0/16      → Private

127.0.0.0/8         → Loopback
169.254.0.0/16      → Link-local
```

### Commands

```bash
ip -4 addr
ip route
```

Windows:

```cmd
ipconfig
ipconfig /all
route print
```

---

# 32. Practical Lab Ideas

### Lab 1 — Find Your IPv4 Information

On Kali:

```bash
ip -4 addr
```

Identify:

```text
IPv4 address
CIDR
Interface
```

Then:

```bash
ip route
```

Identify the default gateway.

---

### Lab 2 — Classify Addresses

Create a list:

```text
10.1.1.5
172.16.5.10
172.32.5.10
192.168.1.20
8.8.8.8
127.0.0.1
```

For each identify:

```text
Private?
Loopback?
Public/other?
Traditional class?
```

---

### Lab 3 — Build Your Mental Network Map

For your **own lab network**, document:

```text
Your machine
     ↓
IPv4 address
     ↓
Subnet/CIDR
     ↓
Default gateway
     ↓
DNS server
     ↓
Other authorized lab systems
```

This is excellent preparation for network VAPT.

---

# 33. Final Mental Model

Don't memorize IPv4 as random numbers.

Think:

```text
                 IPv4
                   │
              32 bits
                   │
             4 × 8-bit
              octets
                   │
        ┌──────────┴──────────┐
        │                     │
     Network                 Host
        │                     │
     Subnet                  Device
     /CIDR                  Address
        │
        ↓
 Routing / Communication
        │
        ↓
     VAPT Recon
        │
        ├── Identify hosts
        ├── Understand boundaries
        ├── Identify exposure
        ├── Enumerate services
        └── Assess security controls
```

## Key Takeaways

1. **IPv4 = 32 bits = 4 octets.**
2. Each octet ranges from **0–255**.
3. The subnet mask/CIDR separates **network and host portions**.
4. Class A/B/C are **traditional classful concepts**; modern networks use **CIDR**.
5. `10.0.0.0/8`, `172.16.0.0/12`, and `192.168.0.0/16` are RFC1918 private ranges.
6. `127.0.0.0/8` is loopback.
7. `169.254.0.0/16` is IPv4 link-local.
8. `0.0.0.0` is context-dependent; it is not simply "the Internet."
9. IP addressing is fundamental to **host discovery, network mapping, segmentation analysis, and VAPT reconnaissance**.
10. **Learn CIDR and subnetting next**—that's where IPv4 becomes genuinely powerful.



**Why not 10/10?** The next level is hands-on subnetting: calculating network address, broadcast address, usable ranges, host counts, and CIDR boundaries quickly. That belongs in your next files rather than overloading this fundamentals note.
