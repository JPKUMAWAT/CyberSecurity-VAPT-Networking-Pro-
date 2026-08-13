

## 1. What Is Subnetting?

**Subnetting** means dividing one large IPv4 network into multiple smaller logical networks called **subnets**.

### Simple example

Suppose you have:

```text
192.168.1.0/24
```

A `/24` network normally contains **256 total IPv4 addresses**.

Instead of keeping one large network, we can divide it:

```text
192.168.1.0/25
        ↓
 ┌───────────────┐
 │ Subnet 1      │
 │ 192.168.1.0/25│
 └───────────────┘

192.168.1.128/25
        ↓
 ┌─────────────────┐
 │ Subnet 2        │
 │192.168.1.128/25 │
 └─────────────────┘
```

So:

```text
1 large network
      ↓
2 smaller networks
```

---

# 2. Why Do We Need Subnetting?

Subnetting is useful for:

* Network organization
* Reducing broadcast domains
* IP address management
* Network segmentation
* Improving security
* Separating departments
* Controlling access between networks
* Efficient IP allocation

### Real-world example

A company may have:

```text
Company Network
│
├── HR
├── Finance
├── Developers
├── Servers
├── Guest Wi-Fi
└── Security Team
```

Instead of putting everyone into:

```text
192.168.1.0/24
```

the network can be divided into separate subnets.

This makes security controls and monitoring easier.

---

# 3. IPv4 Basics

An IPv4 address contains **32 bits**.

Example:

```text
192.168.1.10
```

Binary representation:

```text
11000000.10101000.00000001.00001010
```

There are four octets:

```text
192    .    168    .    1    .    10
│           │           │          │
8 bits     8 bits      8 bits     8 bits
```

Therefore:

```text
8 + 8 + 8 + 8 = 32 bits
```

---

# 4. Network Bits vs Host Bits

This is the **most important concept in subnetting**.

Consider:

```text
192.168.1.10/24
```

`/24` means:

```text
24 bits = Network portion
8 bits  = Host portion
```

Diagram:

```text
Network bits                 Host bits
<------------------------> <-------->
11111111.11111111.11111111.00000000
       24 bits                 8 bits
```

Therefore:

```text
Network = 192.168.1
Host    = last octet
```

---

# 5. CIDR Notation

CIDR represents the number of network bits.

Examples:

```text
/8
/16
/24
/25
/26
/27
/28
/29
/30
```

Example:

```text
192.168.1.0/24
```

means:

```text
24 network bits
8 host bits
```

---

# 6. The Subnet Mask

A `/24` corresponds to:

```text
255.255.255.0
```

Binary:

```text
11111111.11111111.11111111.00000000
```

Some important masks:

| CIDR | Subnet Mask     |
| ---- | --------------- |
| /8   | 255.0.0.0       |
| /16  | 255.255.0.0     |
| /24  | 255.255.255.0   |
| /25  | 255.255.255.128 |
| /26  | 255.255.255.192 |
| /27  | 255.255.255.224 |
| /28  | 255.255.255.240 |
| /29  | 255.255.255.248 |
| /30  | 255.255.255.252 |

### Must remember

```text
/25 → 128
/26 → 192
/27 → 224
/28 → 240
/29 → 248
/30 → 252
```

---

# 7. The Basic Host Formula

For a normal IPv4 subnet:

```text
Host bits = 32 - CIDR
```

Total addresses:

```text
2^(host bits)
```

Traditional usable host addresses:

```text
2^(host bits) - 2
```

The `-2` accounts for:

```text
Network address
Broadcast address
```

### Example: `/24`

```text
Host bits = 32 - 24
          = 8
```

Total:

```text
2^8 = 256
```

Traditional usable:

```text
256 - 2 = 254
```

So:

```text
/24
256 total
254 traditional usable hosts
```

> **Important:** `/31` and `/32` are special cases and don't follow the ordinary `-2` host rule in the same way. `/31` is commonly used for point-to-point links, while `/32` identifies a single address.

---

# 8. Subnetting Example 1 — `/24` to `/25`

Start with:

```text
192.168.1.0/24
```

We want:

```text
2 subnets
```

To create 2 subnets, borrow **1 host bit**.

```text
/24 → /25
```

Now:

```text
32 - 25 = 7 host bits
```

Total addresses per subnet:

```text
2^7 = 128
```

Traditional usable:

```text
128 - 2 = 126
```

---

## Subnet 1

```text
192.168.1.0/25
```

Range:

```text
192.168.1.0
       ↓
192.168.1.127
```

Therefore:

```text
Network Address = 192.168.1.0
First Host       = 192.168.1.1
Last Host        = 192.168.1.126
Broadcast        = 192.168.1.127
```

---

## Subnet 2

```text
192.168.1.128/25
```

Range:

```text
192.168.1.128
       ↓
192.168.1.255
```

Therefore:

```text
Network Address = 192.168.1.128
First Host       = 192.168.1.129
Last Host        = 192.168.1.254
Broadcast        = 192.168.1.255
```

---

# 9. Subnetting Example 2 — `/24` to `/26`

Suppose:

```text
192.168.1.0/24
```

We need:

```text
4 subnets
```

Number of borrowed bits:

```text
2
```

Therefore:

```text
/24 + 2 = /26
```

Host bits:

```text
32 - 26 = 6
```

Total addresses:

```text
2^6 = 64
```

Traditional usable hosts:

```text
64 - 2 = 62
```

---

## Four Subnets

| Subnet | Network          | Usable Range | Broadcast |
| ------ | ---------------- | ------------ | --------- |
| 1      | 192.168.1.0/26   | .1 – .62     | .63       |
| 2      | 192.168.1.64/26  | .65 – .126   | .127      |
| 3      | 192.168.1.128/26 | .129 – .190  | .191      |
| 4      | 192.168.1.192/26 | .193 – .254  | .255      |

### Pattern

The subnet increment is:

```text
256 - 192 = 64
```

So networks start at:

```text
0
64
128
192
```

---

# 10. The Block Size Method ⭐

This is one of the fastest ways to solve subnetting questions.

Formula:

```text
Block Size = 256 - subnet-mask-octet
```

Example:

```text
/26
```

Mask:

```text
255.255.255.192
```

Block size:

```text
256 - 192
= 64
```

Therefore subnet boundaries are:

```text
0
64
128
192
```

---

# 11. Example — Find the Network Address

Given:

```text
192.168.10.77/26
```

### Step 1 — Find mask

```text
/26 = 255.255.255.192
```

### Step 2 — Block size

```text
256 - 192 = 64
```

### Step 3 — Find the range containing 77

Subnet boundaries:

```text
0
64
128
192
```

`77` falls between:

```text
64 → 127
```

Therefore:

```text
Network Address = 192.168.10.64
Broadcast        = 192.168.10.127
```

Usable:

```text
192.168.10.65
        ↓
192.168.10.126
```

---

# 12. Example — `/27`

Given:

```text
192.168.1.100/27
```

Mask:

```text
255.255.255.224
```

Block size:

```text
256 - 224 = 32
```

Boundaries:

```text
0
32
64
96
128
160
192
224
```

`100` falls into:

```text
96 → 127
```

Therefore:

```text
Network    = 192.168.1.96
First Host = 192.168.1.97
Last Host  = 192.168.1.126
Broadcast  = 192.168.1.127
```

---

# 13. Subnetting Cheat Table

| CIDR | Block Size | Total IPs | Traditional Usable |
| ---- | ---------: | --------: | -----------------: |
| /24  |        256 |       256 |                254 |
| /25  |        128 |       128 |                126 |
| /26  |         64 |        64 |                 62 |
| /27  |         32 |        32 |                 30 |
| /28  |         16 |        16 |                 14 |
| /29  |          8 |         8 |                  6 |
| /30  |          4 |         4 |                  2 |

This table is **very important for exams/interviews**.

---

# 14. How Many Subnets?

Formula:

```text
Number of subnets = 2^borrowed bits
```

Example:

```text
/24 → /27
```

Borrowed:

```text
27 - 24 = 3 bits
```

Therefore:

```text
2^3 = 8 subnets
```

---

# 15. How Many Hosts Per Subnet?

For normal traditional IPv4 subnetting:

```text
Host bits = 32 - CIDR
```

Then:

```text
Usable hosts = 2^host bits - 2
```

Example:

```text
/27
```

Host bits:

```text
32 - 27 = 5
```

Total:

```text
2^5 = 32
```

Traditional usable:

```text
32 - 2 = 30
```

---

# 16. VLSM — Variable Length Subnet Mask

**VLSM** allows different subnets to have different sizes.

Example:

```text
Engineering → 100 hosts
HR           → 20 hosts
Management   → 10 hosts
Point-to-point → 2 hosts
```

Using the same `/24` for every department would waste addresses.

VLSM allows us to allocate appropriate subnet sizes.

Example concept:

```text
/25 → ~126 traditional usable
/27 → ~30
/28 → ~14
/30 → 2
```

This is much more efficient.

---

# 17. VLSM Example

Suppose we have:

```text
192.168.10.0/24
```

Requirements:

```text
Department A → 100 hosts
Department B → 50 hosts
Department C → 20 hosts
```

Allocate largest first.

### A — 100 hosts

Need at least 100 usable:

```text
/25 → 126 usable
```

Allocate:

```text
192.168.10.0/25
```

---

### B — 50 hosts

Need:

```text
/26 → 62 usable
```

Allocate:

```text
192.168.10.128/26
```

---

### C — 20 hosts

Need:

```text
/27 → 30 usable
```

Allocate:

```text
192.168.10.192/27
```

Remaining space:

```text
192.168.10.224 – 192.168.10.255
```

This can be used for additional networks.

---

# 18. Subnetting and Network Security 🔐

Subnetting itself is **not a firewall** and does not automatically secure a network.

However, subnetting can provide the network structure needed for segmentation.

Example:

```text
                Router / Firewall
                       │
        ┌──────────────┼──────────────┐
        │              │              │
      VLAN 10        VLAN 20        VLAN 30
        │              │              │
       HR           Servers         Guests
```

Firewall/ACL rules can then control traffic between these network segments.

Example policy:

```text
Guest → Internet       ALLOW
Guest → Internal       DENY

HR → Servers           ALLOW
HR → Guest              DENY
```

### VAPT relevance

During an authorized assessment, you may evaluate:

* Network segmentation
* Unnecessary routing
* Firewall/ACL effectiveness
* Exposed services
* Internal attack surface
* Guest-to-internal isolation
* Server subnet exposure

---

# 19. Subnetting in VAPT

Suppose reconnaissance identifies:

```text
10.10.0.0/16
```

A pentester should understand the scope and network boundaries.

For example:

```text
10.10.1.0/24 → User network
10.10.2.0/24 → Server network
10.10.3.0/24 → Management network
```

This helps answer:

```text
Which hosts belong to which network?
Which systems are supposed to communicate?
Where are network boundaries?
Are sensitive systems properly segmented?
```

---

# 20. Enumeration Perspective

Subnet information can help organize authorized assessment results:

```text
Network
   ↓
Subnet
   ↓
Host
   ↓
Port
   ↓
Service
   ↓
Application
   ↓
Potential vulnerability
```

For example:

```text
10.10.2.0/24
      ↓
10.10.2.15
      ↓
443/tcp
      ↓
HTTPS
      ↓
Web Application
```

Subnetting therefore connects directly with network enumeration.

---

# 21. Linux Practical Commands

On a Linux/Kali lab machine:

```bash
ip addr
```

Shows network interfaces and IP addresses.

```bash
ip route
```

Shows routing information.

Example:

```text
192.168.1.0/24 dev eth0
default via 192.168.1.1
```

You can interpret:

```text
192.168.1.0/24
```

as the directly connected network.

---

# 22. Windows Practical Commands

```powershell
ipconfig
```

Shows IP configuration.

More detailed:

```powershell
ipconfig /all
```

Routing table:

```powershell
route print
```

These are useful for understanding your **own authorized lab network**.

---

# 23. Common Beginner Mistakes ❌

### Mistake 1 — Confusing `/24` with 24 addresses

Wrong:

```text
/24 = 24 IPs
```

Correct:

```text
/24 = 24 network bits
```

---

### Mistake 2 — Forgetting network and broadcast

For a traditional `/26`:

```text
64 total
62 usable
```

Not 64 usable host addresses.

---

### Mistake 3 — Wrong block size

For:

```text
/27
```

Mask:

```text
255.255.255.224
```

Block:

```text
256 - 224 = 32
```

Not 224.

---

### Mistake 4 — Finding the wrong subnet

Given:

```text
192.168.1.70/27
```

Don't simply assume:

```text
network = .70
```

Find the nearest subnet boundary:

```text
0, 32, 64, 96...
```

Therefore:

```text
70 → 64–95
```

Network:

```text
192.168.1.64
```

---

# 24. Scenario-Based Questions

### Scenario 1

You have:

```text
192.168.1.0/24
```

and need 4 equal subnets.

What prefix should you use?

**Answer:**

```text
/26
```

Because:

```text
2² = 4
```

---

### Scenario 2

You have:

```text
192.168.10.70/27
```

Find the network address.

Boundaries:

```text
0, 32, 64, 96...
```

70 belongs to:

```text
64–95
```

Answer:

```text
192.168.10.64
```

---

### Scenario 3

A company needs around 50 hosts in one subnet.

Which is more appropriate?

```text
/27 → 30 usable
/26 → 62 usable
```

Answer:

```text
/26
```

---

# 25. Interview Questions

### Q1. What is subnetting?

Dividing a larger IP network into smaller logical networks.

### Q2. What does `/24` mean?

24 network bits and 8 host bits.

### Q3. How many total addresses are in `/26`?

```text
2^6 = 64
```

### Q4. Traditional usable hosts in `/26`?

```text
62
```

### Q5. What is the subnet mask of `/27`?

```text
255.255.255.224
```

### Q6. What is block size for `/28`?

```text
256 - 240 = 16
```

### Q7. What is VLSM?

Variable Length Subnet Masking; it allows different subnet sizes within a larger address space.

### Q8. Does subnetting itself provide security?

**No.** It helps create network boundaries, while ACLs, firewalls, routing policies, and other controls enforce security between them.

### Q9. Why is subnetting relevant to VAPT?

It helps understand network boundaries, scope, segmentation, reachable hosts, and attack surface.

### Q10. What are network and broadcast addresses?

The network address identifies the subnet; the broadcast address is used to reach all hosts on a traditional IPv4 subnet.

---

# 26. MCQ Practice

### 1. IPv4 contains how many bits?

A. 16
B. 32
C. 64
D. 128

**Answer: B**

---

### 2. `/24` has how many host bits?

A. 4
B. 8
C. 16
D. 24

**Answer: B**

---

### 3. `/26` contains how many total addresses?

A. 32
B. 64
C. 128
D. 256

**Answer: B**

---

### 4. `/27` subnet mask is:

A. 255.255.255.192
B. 255.255.255.224
C. 255.255.255.240
D. 255.255.255.128

**Answer: B**

---

### 5. Block size of `/28` is:

A. 8
B. 16
C. 32
D. 64

**Answer: B**

---

### 6. `/24` divided into 4 equal subnets becomes:

A. `/25`
B. `/26`
C. `/27`
D. `/28`

**Answer: B**

---

### 7. What does VLSM provide?

A. Encryption
B. Variable subnet sizes
C. DNS resolution
D. Packet encryption

**Answer: B**

---

### 8. Which command shows routing information on Linux?

A. `ip route`
B. `pwd`
C. `ls`
D. `whoami`

**Answer: A**

---

### 9. Which Windows command shows IP configuration?

A. `route print`
B. `ipconfig`
C. `netstat`
D. `tracert`

**Answer: B**

---

### 10. Does subnetting automatically block attackers?

A. Yes
B. No
C. Only with IPv6
D. Only with DNS

**Answer: B**

---

# 27. Must-Memorize Cheat Sheet 🧠

```text
IPv4 = 32 bits

Host bits = 32 - prefix

Total addresses = 2^host bits

Traditional usable hosts = 2^host bits - 2

Subnets = 2^borrowed bits

Block size = 256 - subnet-mask octet
```

### CIDR Table

```text
/24 → 255.255.255.0   → 256 total → 254 usable
/25 → 255.255.255.128 → 128 total → 126 usable
/26 → 255.255.255.192 → 64 total  → 62 usable
/27 → 255.255.255.224 → 32 total  → 30 usable
/28 → 255.255.255.240 → 16 total  → 14 usable
/29 → 255.255.255.248 → 8 total   → 6 usable
/30 → 255.255.255.252 → 4 total   → 2 usable
```

### Fast pattern

```text
/25 → 128 block
/26 → 64
/27 → 32
/28 → 16
/29 → 8
/30 → 4
```

---

# 28. Practical Lab Ideas 🧪

### Lab 1 — Manual Subnetting

Solve:

```text
192.168.1.0/24 → 4 subnets
```

Find:

* Network addresses
* Broadcast addresses
* First host
* Last host
* Usable hosts

---

### Lab 2 — Find the Network

For each:

```text
192.168.1.73/26
10.10.10.130/27
172.16.5.200/28
```

Calculate:

```text
Network
First host
Last host
Broadcast
```

---

### Lab 3 — VLSM Design

Design a network for:

```text
Engineering → 100 hosts
HR → 40 hosts
Security → 20 hosts
Management → 10 hosts
```

Start with:

```text
192.168.50.0/24
```

Allocate the largest subnet first.

---

### Lab 4 — Kali/Windows Network Observation

On Kali:

```bash
ip addr
ip route
```

On Windows:

```powershell
ipconfig /all
route print
```

Identify:

```text
Your IP
Subnet mask/prefix
Network
Default gateway
```

Do this only on systems/networks you own or are authorized to test.

---

# 29. Advanced Concepts to Learn Next

Once basic subnetting is comfortable, move to:

```text
Subnetting
   ↓
CIDR
   ↓
VLSM
   ↓
Route summarization
   ↓
Routing tables
   ↓
Longest Prefix Match
   ↓
VLANs
   ↓
Inter-VLAN routing
   ↓
ACLs
   ↓
Firewall segmentation
   ↓
IPv6 subnetting
```

These concepts are much more useful for real networking/VAPT than memorizing subnet tables alone.

---

# 30. Final Mental Model

Think of subnetting like dividing a large building:

```text
Large Building
192.168.1.0/24
        │
        ├── Floor 1 → /26
        ├── Floor 2 → /26
        ├── Floor 3 → /26
        └── Floor 4 → /26
```

Each subnet has:

```text
Network Address
      ↓
Usable Hosts
      ↓
Broadcast Address
```

And in a security environment:

```text
IP Address
    ↓
Subnet
    ↓
Network Boundary
    ↓
Routing / ACL / Firewall
    ↓
Reachable Systems
    ↓
Services
    ↓
Attack Surface
```


