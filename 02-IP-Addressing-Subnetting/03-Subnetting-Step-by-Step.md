# 03 – Subnetting Step-by-Step

> **Goal:** Understand subnetting from zero and become comfortable solving subnetting questions manually.

---

# 1. What is Subnetting?

**Subnetting** means dividing one large IP network into smaller networks called **subnets**.

### Simple example

Suppose you have:

```text
192.168.1.0/24
```

This is one network containing 256 addresses.

Instead of using the whole network for one purpose, we can divide it:

```text
192.168.1.0/25
192.168.1.128/25
```

Now we have **2 smaller networks**.

### Why subnet?

Subnetting helps with:

* Better IP address management
* Reducing broadcast traffic
* Network segmentation
* Security isolation
* Efficient IP allocation
* Designing enterprise networks
* Understanding routing
* VAPT network enumeration

---

# 2. Important Terms

Before learning subnetting, understand these terms.

| Term              | Meaning                                       |
| ----------------- | --------------------------------------------- |
| Network Address   | Identifies the network                        |
| Host Address      | Identifies a device inside the network        |
| Broadcast Address | Used to reach all hosts in a subnet           |
| Subnet Mask       | Defines network and host portions             |
| Prefix Length     | CIDR notation such as `/24`                   |
| Usable IPs        | Addresses normally assigned to hosts          |
| Subnet            | Smaller network created from a larger network |

---

# 3. IPv4 Refresher

IPv4 has **32 bits**.

Example:

```text
192.168.1.10
```

Each section is called an **octet**.

```text
192     .     168     .     1     .     10
 8 bits       8 bits       8 bits      8 bits
```

Therefore:

```text
8 + 8 + 8 + 8 = 32 bits
```

---

# 4. Binary Basics

Subnetting becomes much easier when you understand binary.

Each IPv4 octet has 8 bits.

The bit values are:

```text
128 64 32 16 8 4 2 1
```

Example:

```text
192
```

Convert to binary:

```text
192 = 128 + 64
```

Therefore:

```text
192 = 11000000
```

Another example:

```text
168 = 128 + 32 + 8
```

Therefore:

```text
168 = 10101000
```

So:

```text
192.168.1.10
```

becomes:

```text
11000000.10101000.00000001.00001010
```

---

# 5. Subnet Mask

A subnet mask tells us which bits belong to:

```text
Network portion
```

and which belong to:

```text
Host portion
```

Common masks:

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

These are extremely important to memorize.

---

# 6. Network Bits vs Host Bits

Consider:

```text
192.168.1.10/24
```

`/24` means:

```text
24 bits = Network
8 bits  = Host
```

Binary:

```text
11111111.11111111.11111111.00000000
```

Therefore:

```text
Network bits = 24
Host bits    = 8
```

---

# 7. Host Calculation

For normal IPv4 subnets:

```text
Usable Hosts = 2^host_bits - 2
```

Why `-2`?

Because:

```text
1 address = Network address
1 address = Broadcast address
```

### Example: /24

```text
Host bits = 32 - 24
          = 8
```

Therefore:

```text
2^8 - 2
= 256 - 2
= 254 usable hosts
```

So:

```text
/24 → 254 usable hosts
```

---

# 8. Common CIDR Cheat Sheet

| CIDR | Total Addresses | Usable Hosts |
| ---- | --------------: | -----------: |
| /24  |             256 |          254 |
| /25  |             128 |          126 |
| /26  |              64 |           62 |
| /27  |              32 |           30 |
| /28  |              16 |           14 |
| /29  |               8 |            6 |
| /30  |               4 |            2 |

### Easy pattern

Every time the prefix increases by 1:

```text
Number of addresses becomes half.
```

Example:

```text
/24 → 256
/25 → 128
/26 → 64
/27 → 32
/28 → 16
/29 → 8
/30 → 4
```

---

# 9. The Most Important Subnetting Concept: Block Size

Block size tells us where the next subnet starts.

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

Therefore:

```text
256 - 192 = 64
```

Block size = **64**

Subnets start at:

```text
0
64
128
192
```

Therefore:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

---

# 10. Full Example – 192.168.1.50/26

Find:

* Network address
* First usable IP
* Last usable IP
* Broadcast address

### Step 1 – Find mask

```text
/26 = 255.255.255.192
```

### Step 2 – Calculate block size

```text
256 - 192 = 64
```

### Step 3 – Find subnet ranges

```text
0–63
64–127
128–191
192–255
```

Our IP is:

```text
192.168.1.50
```

`50` belongs to:

```text
0–63
```

Therefore:

### Network

```text
192.168.1.0
```

### First usable

```text
192.168.1.1
```

### Last usable

```text
192.168.1.62
```

### Broadcast

```text
192.168.1.63
```

### Final answer

```text
Network:    192.168.1.0
First Host: 192.168.1.1
Last Host:  192.168.1.62
Broadcast:  192.168.1.63
```

---

# 11. Example – 192.168.10.75/27

### Step 1

```text
/27 = 255.255.255.224
```

### Step 2

```text
256 - 224 = 32
```

Block size:

```text
32
```

Subnets:

```text
0–31
32–63
64–95
96–127
128–159
160–191
192–223
224–255
```

IP:

```text
192.168.10.75
```

75 falls inside:

```text
64–95
```

Therefore:

```text
Network       = 192.168.10.64
First Host    = 192.168.10.65
Last Host     = 192.168.10.94
Broadcast     = 192.168.10.95
```

---

# 12. Example – 10.10.20.130/28

### Step 1

```text
/28 = 255.255.255.240
```

### Step 2

```text
256 - 240 = 16
```

Subnets:

```text
0–15
16–31
32–47
48–63
64–79
80–95
96–111
112–127
128–143
144–159
...
```

130 belongs to:

```text
128–143
```

Therefore:

```text
Network       = 10.10.20.128
First Host    = 10.10.20.129
Last Host     = 10.10.20.142
Broadcast     = 10.10.20.143
```

---

# 13. Fast Subnetting Method

For most `/24`-based questions, use this 5-step method:

### Step 1

Find the subnet mask.

### Step 2

Find the interesting octet.

### Step 3

Calculate:

```text
256 - mask value
```

### Step 4

Find the range containing the IP.

### Step 5

Identify:

```text
Network
First Host
Last Host
Broadcast
```

---

# 14. Finding the Interesting Octet

Example:

```text
192.168.10.75/27
```

Mask:

```text
255.255.255.224
```

The first three octets are:

```text
255.255.255
```

The changing octet is:

```text
224
```

This is the **interesting octet**.

Subnetting calculations happen there.

---

# 15. Subnetting by Borrowing Bits

Subnetting works by taking bits from the host portion and using them as network bits.

Suppose:

```text
192.168.1.0/24
```

Originally:

```text
Network = 24 bits
Host    = 8 bits
```

Change to:

```text
/26
```

Now:

```text
Network = 26 bits
Host    = 6 bits
```

We borrowed:

```text
26 - 24 = 2 bits
```

---

# 16. Number of Subnets

Formula:

```text
Number of subnets = 2^borrowed_bits
```

For `/24 → /26`:

```text
Borrowed bits = 2
```

Therefore:

```text
2^2 = 4 subnets
```

So:

```text
192.168.1.0/24
```

can be divided into:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

---

# 17. Subnetting Table

Starting with:

```text
192.168.1.0/24
```

| New CIDR | Subnets | Hosts/Subnet |
| -------- | ------: | -----------: |
| /25      |       2 |          126 |
| /26      |       4 |           62 |
| /27      |       8 |           30 |
| /28      |      16 |           14 |
| /29      |      32 |            6 |
| /30      |      64 |            2 |

---

# 18. VLSM – Variable Length Subnet Masking

VLSM means using different subnet sizes inside the same network.

Example requirements:

```text
Department A → 100 hosts
Department B → 50 hosts
Department C → 20 hosts
Department D → 10 hosts
```

Instead of giving everyone `/24`, we can allocate:

```text
100 hosts → /25
50 hosts  → /26
20 hosts  → /27
10 hosts  → /28
```

This saves IP addresses.

---

# 19. VLSM Thinking

Always allocate the **largest requirement first**.

Example:

```text
100
50
20
10
```

Start with:

```text
100 → /25
```

Then:

```text
50 → /26
```

Then:

```text
20 → /27
```

Then:

```text
10 → /28
```

This is a very important networking design skill.

---

# 20. Subnetting in Cybersecurity

Subnetting is not just a math topic.

It is important in:

### Network Enumeration

Understanding:

```text
10.10.10.0/24
```

means potentially:

```text
10.10.10.1 – 10.10.10.254
```

### Network Segmentation

Organizations may separate:

```text
Users
Servers
Databases
Security systems
Guest devices
```

into different subnets.

### VAPT

Before testing a network, a pentester must understand:

```text
What network am I authorized to test?
```

For example:

```text
10.10.20.0/24
```

is very different from:

```text
10.10.20.0/16
```

A wrong scope can result in testing systems that were not authorized.

---

# 21. Subnetting + Nmap

If you are authorized to assess:

```text
192.168.1.0/24
```

Nmap can work with the CIDR network:

```bash
nmap 192.168.1.0/24
```

This represents the subnet, not one individual host.

For a smaller lab:

```text
192.168.1.0/28
```

the host range is:

```text
192.168.1.1 – 192.168.1.14
```

Only scan systems you own or have explicit permission to test.

---

# 22. Network Address vs Host Address

This is a common exam trap.

For:

```text
192.168.1.64/26
```

`192.168.1.64` is the **network address**.

It normally should not be assigned to a host.

Usable range:

```text
192.168.1.65 – 192.168.1.126
```

Broadcast:

```text
192.168.1.127
```

---

# 23. Broadcast Address

The broadcast address is the **last address of the subnet**.

Example:

```text
192.168.1.64/26
```

Range:

```text
64–127
```

Therefore:

```text
Broadcast = 192.168.1.127
```

---

# 24. First and Last Usable IP

For traditional IPv4 subnetting:

```text
First usable = Network + 1
```

```text
Last usable = Broadcast - 1
```

Example:

```text
Network   = 192.168.1.64
Broadcast = 192.168.1.127
```

Therefore:

```text
First = 192.168.1.65
Last  = 192.168.1.126
```

---

# 25. Common Mistakes

### Mistake 1

Thinking `/26` means 26 hosts.

❌ Wrong.

It means:

```text
26 network bits
```

---

### Mistake 2

Forgetting `-2`.

For a traditional `/26`:

```text
64 total
62 usable
```

---

### Mistake 3

Choosing the wrong subnet range.

For:

```text
192.168.1.75/27
```

don't choose:

```text
64–127
```

because the block size is 32.

Correct:

```text
64–95
```

---

### Mistake 4

Confusing network and broadcast addresses.

Remember:

```text
First = Network
Last = Broadcast
Middle = Hosts
```

---

# 26. Quick Mental Math

Memorize this:

```text
/24 → 256 addresses → 254 hosts
/25 → 128 addresses → 126 hosts
/26 → 64 addresses → 62 hosts
/27 → 32 addresses → 30 hosts
/28 → 16 addresses → 14 hosts
/29 → 8 addresses → 6 hosts
/30 → 4 addresses → 2 hosts
```

And:

```text
/25 → mask 128
/26 → mask 192
/27 → mask 224
/28 → mask 240
/29 → mask 248
/30 → mask 252
```

---

# 27. Practice Questions

## Q1

Find the network address:

```text
192.168.1.70/26
```

### Answer

Block size:

```text
64
```

Ranges:

```text
0–63
64–127
```

70 is inside:

```text
64–127
```

Therefore:

```text
Network = 192.168.1.64
```

---

## Q2

Find the broadcast address:

```text
192.168.10.100/27
```

Block size:

```text
32
```

100 belongs to:

```text
96–127
```

Therefore:

```text
Broadcast = 192.168.10.127
```

---

## Q3

How many usable hosts are available in `/28`?

```text
Host bits = 32 - 28
          = 4
```

```text
2^4 - 2
= 16 - 2
= 14
```

Answer:

```text
14 usable hosts
```

---

## Q4

How many `/26` subnets can be created from `/24`?

Borrowed bits:

```text
26 - 24 = 2
```

Therefore:

```text
2^2 = 4
```

Answer:

```text
4 subnets
```

---

# 28. Mini Challenge

Try solving these **without looking at the answers**.

### Challenge 1

```text
192.168.5.45/27
```

Find:

```text
Network
First Host
Last Host
Broadcast
```

### Challenge 2

```text
10.0.0.130/26
```

Find:

```text
Network
First Host
Last Host
Broadcast
```

### Challenge 3

```text
172.16.20.200/28
```

Find:

```text
Network
First Host
Last Host
Broadcast
```

### Challenge 4

How many usable hosts are available in:

```text
/29
```

### Challenge 5

How many `/27` subnets can be created from a `/24`?

---

# 29. Answers

### Challenge 1

```text
192.168.5.45/27

Network   = 192.168.5.32
First     = 192.168.5.33
Last      = 192.168.5.62
Broadcast = 192.168.5.63
```

### Challenge 2

```text
10.0.0.130/26

Network   = 10.0.0.128
First     = 10.0.0.129
Last      = 10.0.0.190
Broadcast = 10.0.0.191
```

### Challenge 3

```text
172.16.20.200/28

Network   = 172.16.20.192
First     = 172.16.20.193
Last      = 172.16.20.206
Broadcast = 172.16.20.207
```

### Challenge 4

```text
/29

2^3 - 2 = 6
```

Answer:

```text
6 usable hosts
```

### Challenge 5

```text
/24 → /27
Borrowed bits = 3

2^3 = 8
```

Answer:

```text
8 subnets
```

---

# 30. Interview Questions

### Q1. What is subnetting?

Subnetting is dividing a larger IP network into smaller logical networks.

### Q2. Why is subnetting used?

For efficient IP allocation, network organization, performance, segmentation, and security.

### Q3. What does `/24` mean?

It means 24 of the 32 IPv4 bits are network bits.

### Q4. How many usable hosts are in `/24`?

```text
254
```

### Q5. What is the subnet mask of `/26`?

```text
255.255.255.192
```

### Q6. What is block size for `/27`?

```text
256 - 224 = 32
```

### Q7. What is a broadcast address?

The last address in a subnet used for broadcast communication in traditional IPv4 networks.

### Q8. What is VLSM?

Variable Length Subnet Masking — using different subnet sizes according to network requirements.

### Q9. Why is subnetting important for a pentester?

It helps understand network boundaries, scope, segmentation, routing, and the systems potentially inside an authorized assessment range.

### Q10. What is the difference between network address and host address?

The network address identifies the subnet; host addresses identify individual devices within that subnet.

---

# 31. One-Page Revision

```text
IPv4 = 32 bits

Host bits = 32 - Prefix

Total addresses = 2^host_bits

Usable hosts = 2^host_bits - 2

Block Size = 256 - interesting-octet mask

First Host = Network + 1

Last Host = Broadcast - 1

Broadcast = Last address of subnet
```

### Must memorize

```text
/24 → 255.255.255.0   → 254 hosts
/25 → 255.255.255.128 → 126 hosts
/26 → 255.255.255.192 → 62 hosts
/27 → 255.255.255.224 → 30 hosts
/28 → 255.255.255.240 → 14 hosts
/29 → 255.255.255.248 → 6 hosts
/30 → 255.255.255.252 → 2 hosts
```

---

# 32. Pentester Mindset

Don't learn subnetting only as a JEE-style formula topic.

When you see:

```text
10.10.20.0/24
```

your brain should immediately understand:

```text
Network
    ↓
10.10.20.0/24
    ↓
Subnet mask
    ↓
255.255.255.0
    ↓
Potential host range
    ↓
10.10.20.1–10.10.20.254
    ↓
Understand authorized scope
    ↓
Identify network segments
    ↓
Perform authorized enumeration
```

The goal is to understand **what the network actually represents**, not simply calculate an answer.

---

# Final Takeaway

Subnetting becomes easy when you follow the same process every time:

```text
CIDR
 ↓
Subnet Mask
 ↓
Interesting Octet
 ↓
Block Size
 ↓
Find Range
 ↓
Network Address
 ↓
First Host
 ↓
Last Host
 ↓
Broadcast
```

Master this flow and `/25` through `/30` subnetting questions become much faster.

**Next recommended topic:** `04-Subnetting-Practice-Problems-100.md` — practice is what turns subnetting knowledge into speed.
