
# CIDR Notation Deep Dive — Complete Notes

> **Goal:** CIDR ko zero se samajhna, subnet size calculate karna, network/broadcast/host range find karna, aur VAPT/network reconnaissance me practically use karna.

---

# 1. What is CIDR?

**CIDR** stands for:

> **Classless Inter-Domain Routing**

CIDR is a modern method of representing IP networks using a **prefix length**.

Example:

```text
192.168.1.0/24
```

Here:

```text
192.168.1.0 = Network address
/24          = Prefix length
```

The `/24` tells us:

> The first **24 bits** belong to the network portion.

The remaining:

```text
32 - 24 = 8 bits
```

are available for hosts.

---

# 2. Why CIDR Was Needed

Earlier, IPv4 used classful addressing:

```text
Class A → /8
Class B → /16
Class C → /24
```

This was inefficient.

Imagine an organization needs around 500 addresses.

A Class C network gives only:

```text
254 usable hosts
```

A Class B network gives:

```text
65,534 usable hosts
```

That's far more than needed.

CIDR allows networks to use more appropriate sizes:

```text
/23
/24
/25
/26
/27
...
```

This reduces address wastage and provides flexible network design.

---

# 3. The Most Important Concept

IPv4 always contains:

```text
32 bits
```

CIDR tells us how many bits belong to the network.

Formula:

```text
Host bits = 32 - Prefix
```

Example:

```text
192.168.1.0/24
```

Therefore:

```text
Host bits = 32 - 24
          = 8
```

Another example:

```text
10.0.0.0/16
```

```text
Host bits = 32 - 16
          = 16
```

---

# 4. CIDR vs Subnet Mask

CIDR:

```text
192.168.1.0/24
```

Subnet mask:

```text
255.255.255.0
```

They represent the same network boundary.

### Common Examples

| CIDR  | Subnet Mask       |
| ----- | ----------------- |
| `/8`  | `255.0.0.0`       |
| `/16` | `255.255.0.0`     |
| `/24` | `255.255.255.0`   |
| `/25` | `255.255.255.128` |
| `/26` | `255.255.255.192` |
| `/27` | `255.255.255.224` |
| `/28` | `255.255.255.240` |
| `/29` | `255.255.255.248` |
| `/30` | `255.255.255.252` |

These are worth learning gradually rather than blindly memorizing.

---

# 5. Understanding `/24`

Consider:

```text
192.168.1.0/24
```

IPv4 has 32 bits.

```text
Network bits = 24
Host bits    = 8
```

Binary structure:

```text
11111111.11111111.11111111.00000000
```

The first 24 bits are network bits.

The last 8 bits are host bits.

Therefore:

```text
Network = 192.168.1
Host    = last octet
```

---

# 6. Number of Addresses

The most important formula:

```text
Total addresses = 2^(host bits)
```

For `/24`:

```text
Host bits = 32 - 24
          = 8

Total = 2^8
      = 256
```

So a `/24` contains:

```text
256 total addresses
```

Traditionally, two are reserved:

```text
Network address
Broadcast address
```

Therefore:

```text
Usable hosts = 256 - 2
             = 254
```

---

# 7. Host Calculation Formula

For ordinary IPv4 subnets:

```text
Usable hosts = 2^(32 - prefix) - 2
```

Example:

```text
/26
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

# 8. CIDR Host Table

|  CIDR | Host Bits | Total Addresses | Traditional Usable Hosts |
| ----: | --------: | --------------: | -----------------------: |
| `/24` |         8 |             256 |                      254 |
| `/25` |         7 |             128 |                      126 |
| `/26` |         6 |              64 |                       62 |
| `/27` |         5 |              32 |                       30 |
| `/28` |         4 |              16 |                       14 |
| `/29` |         3 |               8 |                        6 |
| `/30` |         2 |               4 |                        2 |

### Important

Modern networking has special cases such as `/31` point-to-point networks and `/32` host routes, so the traditional `-2` rule does not apply universally.

---

# 9. `/25` Explained

Take:

```text
192.168.1.0/25
```

Host bits:

```text
32 - 25 = 7
```

Total:

```text
2^7 = 128
```

Traditional usable:

```text
128 - 2 = 126
```

Subnet mask:

```text
255.255.255.128
```

Because:

```text
128 = 10000000
```

The `/25` divides a `/24` into **two equal subnets**.

```text
Subnet 1:
192.168.1.0 → 192.168.1.127

Subnet 2:
192.168.1.128 → 192.168.1.255
```

---

# 10. `/26` Explained

```text
192.168.1.0/26
```

Host bits:

```text
32 - 26 = 6
```

Total:

```text
2^6 = 64
```

Traditional usable:

```text
62
```

Subnet mask:

```text
255.255.255.192
```

A `/24` can be divided into four `/26` networks:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

---

# 11. `/27` Explained

```text
192.168.1.0/27
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
30
```

The `/24` can be divided into:

```text
192.168.1.0/27
192.168.1.32/27
192.168.1.64/27
192.168.1.96/27
192.168.1.128/27
192.168.1.160/27
192.168.1.192/27
192.168.1.224/27
```

That's:

```text
8 subnets
```

---

# 12. The "Block Size" Method

This is one of the easiest ways to solve subnetting questions.

Formula:

```text
Block Size = 256 - subnet mask value
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

Therefore subnet boundaries occur every 64:

```text
0
64
128
192
```

So:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

---

# 13. Finding Network Address — Example 1

Given:

```text
IP = 192.168.1.70/26
```

Step 1:

```text
/26 → mask = 255.255.255.192
```

Step 2:

```text
Block size = 256 - 192
           = 64
```

Subnet boundaries:

```text
0
64
128
192
```

70 falls between:

```text
64 → 127
```

Therefore:

```text
Network Address = 192.168.1.64
```

Broadcast:

```text
192.168.1.127
```

Traditional usable range:

```text
192.168.1.65
→
192.168.1.126
```

---

# 14. Finding Network Address — Example 2

Given:

```text
IP = 192.168.1.150/27
```

`/27` mask:

```text
255.255.255.224
```

Block size:

```text
256 - 224
= 32
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

150 falls inside:

```text
128 → 159
```

Therefore:

```text
Network = 192.168.1.128
Broadcast = 192.168.1.159
Usable = 192.168.1.129 → 192.168.1.158
```

---

# 15. Finding Network Address — Example 3

Given:

```text
10.20.50.200/28
```

`/28`:

```text
Mask = 255.255.255.240
```

Block size:

```text
256 - 240
= 16
```

Boundaries:

```text
0
16
32
48
64
80
96
112
128
144
160
176
192
208
224
240
```

200 falls into:

```text
192 → 207
```

Therefore:

```text
Network:
10.20.50.192

Broadcast:
10.20.50.207

Usable:
10.20.50.193
→
10.20.50.206
```

---

# 16. Subnet Mask Binary Understanding

Subnet masks are made of:

```text
1 = Network bit
0 = Host bit
```

Example `/24`:

```text
11111111.11111111.11111111.00000000
```

Example `/26`:

```text
11111111.11111111.11111111.11000000
```

The first:

```text
26 bits = 1
```

and remaining:

```text
6 bits = 0
```

---

# 17. Prefix Length Meaning

| Prefix | Network Bits | Host Bits |
| -----: | -----------: | --------: |
|   `/8` |            8 |        24 |
|  `/16` |           16 |        16 |
|  `/24` |           24 |         8 |
|  `/25` |           25 |         7 |
|  `/26` |           26 |         6 |
|  `/27` |           27 |         5 |
|  `/28` |           28 |         4 |
|  `/29` |           29 |         3 |
|  `/30` |           30 |         2 |
|  `/32` |           32 |         0 |

### Remember

As the prefix gets larger:

```text
/24 → /25 → /26 → /27
```

Network becomes **smaller**.

---

# 18. Subnetting Relationship

Suppose:

```text
192.168.1.0/24
```

You move to:

```text
/25
```

You borrowed:

```text
1 host bit
```

Number of subnets:

```text
2¹ = 2
```

Move to:

```text
/26
```

Borrowed:

```text
2 bits
```

Subnets:

```text
2² = 4
```

Move to:

```text
/27
```

Borrowed:

```text
3 bits
```

Subnets:

```text
2³ = 8
```

Therefore:

```text
More network bits
        ↓
More subnets
        ↓
Fewer hosts per subnet
```

---

# 19. CIDR `/24` to `/30`

Starting with:

```text
192.168.1.0/24
```

### `/25`

```text
2 subnets
126 usable hosts each
```

### `/26`

```text
4 subnets
62 usable hosts each
```

### `/27`

```text
8 subnets
30 usable hosts each
```

### `/28`

```text
16 subnets
14 usable hosts each
```

### `/29`

```text
32 subnets
6 usable hosts each
```

### `/30`

```text
64 subnets
2 usable addresses each
```

---

# 20. CIDR and VAPT

CIDR is extremely important in network security.

Suppose the authorized scope is:

```text
192.168.10.0/24
```

This represents:

```text
256 total IPv4 addresses
```

A pentester needs to understand the scope before scanning.

The scope can potentially contain:

```text
192.168.10.1
192.168.10.2
192.168.10.3
...
192.168.10.254
```

But **authorization and ROE always determine what may actually be tested**.

CIDR helps the tester understand:

```text
Scope
 ↓
Network
 ↓
Possible hosts
 ↓
Services
 ↓
Attack surface
```

---

# 21. CIDR in Nmap

Nmap accepts CIDR notation.

For an authorized lab:

```bash
nmap 192.168.56.0/24
```

This tells Nmap to work with the specified `/24` network.

Service detection:

```bash
nmap -sV 192.168.56.0/24
```

Specific ports:

```bash
nmap -p 22,80,443 192.168.56.0/24
```

⚠️ Only scan systems/networks you own or have explicit permission to assess.

---

# 22. CIDR in Routing

CIDR is also used in routing tables.

Example:

```text
192.168.1.0/24
```

can represent a route to that network.

Linux:

```bash
ip route
```

Example output may look conceptually like:

```text
192.168.1.0/24 dev eth0
default via 192.168.1.1
```

Meaning:

```text
192.168.1.x
     ↓
eth0
```

Other destinations:

```text
     ↓
default gateway
```

---

# 23. Longest Prefix Match

This is an important advanced networking concept.

Suppose a router has:

```text
10.0.0.0/8
10.10.0.0/16
10.10.10.0/24
```

A packet is going to:

```text
10.10.10.50
```

All three routes could match.

The router chooses the **most specific matching route**:

```text
10.10.10.0/24
```

This is called:

> **Longest Prefix Match**

Think:

```text
More specific prefix
        ↓
More specific route
```

---

# 24. CIDR Aggregation

CIDR can also reduce routing table size by combining networks.

For example, several contiguous networks can sometimes be represented by a larger summarized prefix.

Conceptually:

```text
Network A
Network B
Network C
Network D
    ↓
Route Summary
```

This is called:

> **Route aggregation / summarization**

It helps routers maintain smaller routing tables.

---

# 25. `/32` — Single Host

A `/32` represents one IPv4 address.

Example:

```text
192.168.1.50/32
```

Host bits:

```text
32 - 32 = 0
```

So:

```text
2⁰ = 1 address
```

Common uses:

* Host routes
* Firewall rules
* Routing
* Access-control rules
* Security policies

Example concept:

```text
Allow:
192.168.1.50/32
```

means exactly one IPv4 address.

---

# 26. `/31` — Point-to-Point

A `/31` contains:

```text
2 addresses
```

It is commonly used on point-to-point links where traditional network/broadcast reservations are not needed.

Example:

```text
10.0.0.0/31
```

Addresses:

```text
10.0.0.0
10.0.0.1
```

This is an important exception to the traditional:

```text
2^host - 2
```

rule.

---

# 27. `/30` — Traditional Point-to-Point Subnet

A `/30` has:

```text
4 total addresses
```

Traditional usable:

```text
2
```

Example:

```text
10.0.0.0/30
```

Network:

```text
10.0.0.0
```

Usable:

```text
10.0.0.1
10.0.0.2
```

Broadcast:

```text
10.0.0.3
```

Historically, `/30` was commonly used for point-to-point links.

---

# 28. CIDR Quick Calculation Formula

Given:

```text
IP/prefix
```

### Step 1

Find host bits:

```text
32 - prefix
```

### Step 2

Find total addresses:

```text
2^host_bits
```

### Step 3

For traditional subnets:

```text
usable = total - 2
```

### Step 4

Find subnet mask.

### Step 5

Find block size.

```text
256 - interesting_octet
```

### Step 6

Find the subnet boundary containing the IP.

### Step 7

Find:

```text
Network
Broadcast
Usable range
```

---

# 29. Full Example

Given:

```text
172.16.25.77/27
```

### Step 1 — Host bits

```text
32 - 27 = 5
```

### Step 2 — Total addresses

```text
2^5 = 32
```

### Step 3 — Traditional usable hosts

```text
32 - 2 = 30
```

### Step 4 — Mask

```text
255.255.255.224
```

### Step 5 — Block size

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

77 belongs to:

```text
64 → 95
```

Therefore:

```text
Network:
172.16.25.64

Broadcast:
172.16.25.95

Usable:
172.16.25.65
→
172.16.25.94
```

---

# 30. Another Full Example

Given:

```text
10.10.10.130/26
```

`/26`:

```text
Host bits = 6
Total = 64
Usable = 62
```

Mask:

```text
255.255.255.192
```

Block size:

```text
64
```

Boundaries:

```text
0
64
128
192
```

130 is between:

```text
128 → 191
```

Therefore:

```text
Network:
10.10.10.128

Broadcast:
10.10.10.191

Usable:
10.10.10.129
→
10.10.10.190
```

---

# 31. Common CIDR Mistakes

## Mistake 1

Thinking:

```text
/24 = 24 hosts
```

❌ Wrong.

`/24` means:

```text
24 network bits
```

not 24 hosts.

---

## Mistake 2

Thinking:

```text
/28 = 28 hosts
```

❌ Wrong.

Host bits:

```text
32 - 28 = 4
```

Total:

```text
2⁴ = 16
```

---

## Mistake 3

Using:

```text
2^prefix
```

for host calculation.

❌ Wrong.

Use:

```text
2^(32-prefix)
```

---

## Mistake 4

Always subtracting 2.

For ordinary traditional subnets this works, but `/31` and `/32` are important exceptions.

---

## Mistake 5

Assuming the first three octets always define the network.

❌ Not with CIDR.

For example:

```text
192.168.1.50/26
```

The boundary occurs inside the fourth octet.

---

# 32. Mental Shortcut

Remember this:

```text
CIDR
 ↓
How many network bits?
 ↓
32 - prefix
 ↓
Host bits
 ↓
2^host bits
 ↓
Total addresses
```

For `/24`:

```text
32 - 24 = 8
2^8 = 256
```

For `/26`:

```text
32 - 26 = 6
2^6 = 64
```

For `/28`:

```text
32 - 28 = 4
2^4 = 16
```

---

# 33. Must-Know Table

| CIDR  | Mask              | Total | Traditional Usable |
| ----- | ----------------- | ----: | -----------------: |
| `/24` | `255.255.255.0`   |   256 |                254 |
| `/25` | `255.255.255.128` |   128 |                126 |
| `/26` | `255.255.255.192` |    64 |                 62 |
| `/27` | `255.255.255.224` |    32 |                 30 |
| `/28` | `255.255.255.240` |    16 |                 14 |
| `/29` | `255.255.255.248` |     8 |                  6 |
| `/30` | `255.255.255.252` |     4 |                  2 |
| `/31` | `255.255.255.254` |     2 |       Special case |
| `/32` | `255.255.255.255` |     1 |        Single host |

---

# 34. Interview Questions

### Q1. What does `/24` mean?

It means the first 24 bits are the network prefix.

---

### Q2. How many host bits does `/26` have?

```text
32 - 26 = 6
```

---

### Q3. How many total addresses are in `/26`?

```text
2^6 = 64
```

---

### Q4. How many traditional usable hosts are in `/26`?

```text
64 - 2 = 62
```

---

### Q5. What is the subnet mask of `/27`?

```text
255.255.255.224
```

---

### Q6. What is the block size of `/28`?

Mask:

```text
255.255.255.240
```

Therefore:

```text
256 - 240 = 16
```

---

### Q7. What does `/32` represent?

One IPv4 address/host route.

---

### Q8. What is longest prefix match?

When multiple routes match a destination, routers generally choose the most specific matching prefix.

---

### Q9. Why is CIDR better than classful addressing?

It allows flexible network sizes and reduces address/routing inefficiency.

---

### Q10. What is the difference between `/24` and `/26`?

```text
/24 → 256 total addresses
/26 → 64 total addresses
```

`/26` creates smaller subnets.

---

# 35. Practice Problems

Try these **without looking at the answers**.

### Problem 1

```text
192.168.10.50/24
```

Find:

* Network
* Broadcast
* Usable range
* Total addresses

---

### Problem 2

```text
192.168.10.50/26
```

Find:

* Network
* Broadcast
* Usable range
* Total addresses

---

### Problem 3

```text
192.168.10.100/27
```

Find:

* Network
* Broadcast
* Usable range

---

### Problem 4

```text
10.20.30.200/28
```

Find:

* Network
* Broadcast
* Usable range

---

### Problem 5

How many total addresses are in:

```text
172.16.0.0/20
```

---

# 36. Answers

### Problem 1

```text
192.168.10.50/24

Network:
192.168.10.0

Broadcast:
192.168.10.255

Usable:
192.168.10.1 → 192.168.10.254

Total:
256
```

---

### Problem 2

```text
192.168.10.50/26
```

Boundaries:

```text
0
64
128
192
```

50 belongs to:

```text
0 → 63
```

Therefore:

```text
Network:
192.168.10.0

Broadcast:
192.168.10.63

Usable:
192.168.10.1 → 192.168.10.62

Total:
64
```

---

### Problem 3

```text
192.168.10.100/27
```

Block size:

```text
32
```

100 belongs to:

```text
96 → 127
```

Therefore:

```text
Network:
192.168.10.96

Broadcast:
192.168.10.127

Usable:
192.168.10.97 → 192.168.10.126
```

---

### Problem 4

```text
10.20.30.200/28
```

Block size:

```text
16
```

200 belongs to:

```text
192 → 207
```

Therefore:

```text
Network:
10.20.30.192

Broadcast:
10.20.30.207

Usable:
10.20.30.193 → 10.20.30.206
```

---

### Problem 5

```text
172.16.0.0/20
```

Host bits:

```text
32 - 20 = 12
```

Total:

```text
2^12 = 4096
```

Traditional usable:

```text
4096 - 2 = 4094
```

---

# 37. VAPT Practical Workflow

When you receive an authorized network scope:

```text
192.168.56.0/24
```

Think:

```text
1. What network is this?
          ↓
2. What is the prefix?
          ↓
3. How many addresses?
          ↓
4. What hosts can exist?
          ↓
5. What systems are authorized?
          ↓
6. Discover hosts
          ↓
7. Enumerate ports
          ↓
8. Identify services
          ↓
9. Assess vulnerabilities
          ↓
10. Document findings
```

CIDR is therefore not just a subnetting exam topic.

It helps you understand the **scope and boundaries of a network assessment**.

---

# 38. Advanced Concepts to Learn Next

After mastering this file, learn:

```text
CIDR
 ↓
Subnetting
 ↓
VLSM
 ↓
Route Summarization
 ↓
Longest Prefix Match
 ↓
Routing Tables
 ↓
NAT
 ↓
IPv6
 ↓
Network Recon
 ↓
Nmap
 ↓
Wireshark
```

---

# Final Cheat Sheet

```text
IPv4 = 32 bits

Host bits:
32 - prefix

Total addresses:
2^(host bits)

Traditional usable:
2^(host bits) - 2

/24:
256 total
254 traditional usable

/25:
128 total
126 usable

/26:
64 total
62 usable

/27:
32 total
30 usable

/28:
16 total
14 usable

/29:
8 total
6 usable

/30:
4 total
2 usable

/31:
2 addresses — special case

/32:
1 address
```

### Most Important Mental Model

```text
192.168.1.70/26

        /26
         ↓
26 network bits
         ↓
6 host bits
         ↓
2^6 = 64 addresses
         ↓
Block size = 64
         ↓
70 belongs to 64–127
         ↓
Network = 192.168.1.64
Broadcast = 192.168.1.127
Usable = .65–.126
```

> **Pentester mindset:** CIDR tells you the size and boundary of a network. Before doing network enumeration, understand the CIDR, confirm the authorized scope, and then determine which hosts and services are actually present.
