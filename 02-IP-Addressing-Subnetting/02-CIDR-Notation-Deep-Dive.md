

# 1. What is CIDR?

**CIDR = Classless Inter-Domain Routing**

CIDR is a method of representing an IP network using:

```text
IP Address / Prefix Length
```

Example:

```text
192.168.1.0/24
```

Here:

* `192.168.1.0` → Network address
* `/24` → Prefix length
* `/24` means **24 bits are network bits**
* Remaining `8 bits` are host bits

### Simple Mental Model

Think of an IPv4 address as **32 boxes**:

```text
32 bits total

NNNNNNNN NNNNNNNN NNNNNNNN HHHHHHHH
<------ Network -------> <--- Host --->
          /24
```

`N` = Network bits
`H` = Host bits

---

# 2. Why CIDR Was Introduced

Old IPv4 networking used **classful addressing**:

```text
Class A
Class B
Class C
```

This often wasted addresses.

For example, an organization might need around 500 addresses.

A `/24` gives:

```text
256 total addresses
```

which is too small.

A `/16` gives:

```text
65,536 addresses
```

which is much larger than necessary.

CIDR allows networks to use more precise sizes:

```text
/23
/24
/25
/26
...
```

So CIDR improves:

* Address utilization
* Routing efficiency
* Network design
* Network segmentation

---

# 3. Prefix Length

The number after `/` is called the **prefix length**.

Example:

```text
10.0.0.0/8
```

means:

```text
8 network bits
24 host bits
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

Example:

```text
192.168.1.0/26
```

means:

```text
26 network bits
6 host bits
```

---

# 4. IPv4 Has 32 Bits

Every IPv4 address contains **32 bits**.

Example:

```text
192.168.1.10
```

Binary:

```text
11000000.10101000.00000001.00001010
```

Each octet contains 8 bits:

```text
8 + 8 + 8 + 8 = 32
```

Therefore:

```text
IPv4 = 32 bits
```

---

# 5. CIDR and Subnet Mask

CIDR notation corresponds to a subnet mask.

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
| `/31` | `255.255.255.254` |
| `/32` | `255.255.255.255` |

---

# 6. Network Bits vs Host Bits

This is the **most important CIDR concept**.

Suppose:

```text
192.168.10.0/26
```

IPv4 has 32 bits.

```text
Network bits = 26
Host bits = 32 - 26
           = 6
```

Therefore:

```text
26 network bits
6 host bits
```

---

# 7. Host Calculation

For a normal IPv4 subnet:

```text
Total addresses = 2^host_bits
```

Traditionally, for ordinary subnets:

```text
Usable hosts = 2^host_bits - 2
```

Why `-2`?

Because traditionally:

```text
Network address → reserved
Broadcast address → reserved
```

### Example: /24

```text
Host bits = 32 - 24
          = 8
```

Total:

```text
2^8 = 256
```

Usable:

```text
256 - 2 = 254
```

Therefore:

```text
/24 → 256 total → 254 traditional usable host addresses
```

---

# 8. Important Exception: /31 and /32

Do **not blindly apply `-2` everywhere**.

### /31

`/31` is commonly used for point-to-point links under RFC 3021.

It can provide:

```text
2 addresses
```

without the traditional network/broadcast treatment.

### /32

`/32` represents exactly:

```text
1 IPv4 address
```

It is commonly used for:

* Host routes
* Loopback addresses
* Specific routing entries

---

# 9. CIDR Quick Table

| CIDR  | Host Bits | Total Addresses | Traditional Usable |
| ----- | --------: | --------------: | -----------------: |
| `/24` |         8 |             256 |                254 |
| `/25` |         7 |             128 |                126 |
| `/26` |         6 |              64 |                 62 |
| `/27` |         5 |              32 |                 30 |
| `/28` |         4 |              16 |                 14 |
| `/29` |         3 |               8 |                  6 |
| `/30` |         2 |               4 |                  2 |

### Memory Trick

Every time prefix increases by `1`:

```text
Addresses become HALF.
```

Example:

```text
/24 → 256
/25 → 128
/26 → 64
/27 → 32
/28 → 16
```

---

# 10. CIDR Block Size

For the subnetting octet:

```text
Block Size = 256 - subnet-mask-octet
```

Example:

```text
/26
```

Subnet mask:

```text
255.255.255.192
```

Therefore:

```text
Block Size = 256 - 192
           = 64
```

Networks occur every 64:

```text
0
64
128
192
```

Therefore `/26` subnets inside `192.168.1.0/24` are:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

---

# 11. Finding Network and Broadcast Address

Example:

```text
192.168.1.70/26
```

### Step 1 — Find mask

```text
/26 = 255.255.255.192
```

### Step 2 — Find block size

```text
256 - 192 = 64
```

Network boundaries:

```text
0
64
128
192
```

`70` lies between:

```text
64 → 127
```

Therefore:

```text
Network Address = 192.168.1.64
Broadcast Address = 192.168.1.127
```

Usable range:

```text
192.168.1.65
        ↓
192.168.1.126
```

Total:

```text
64 addresses
```

Traditional usable:

```text
62 hosts
```

---

# 12. CIDR Example — /27

Suppose:

```text
10.10.10.45/27
```

Subnet mask:

```text
255.255.255.224
```

Block size:

```text
256 - 224 = 32
```

Subnet boundaries:

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

`45` belongs to:

```text
32–63
```

Therefore:

```text
Network:
10.10.10.32

Broadcast:
10.10.10.63

Usable:
10.10.10.33
-
10.10.10.62
```

---

# 13. CIDR Visualization

For:

```text
192.168.1.0/26
```

```text
Network                     Host
<------------------------>  <------>
11111111.11111111.11111111.11000000
                         26 bits  6 bits
```

The last octet:

```text
11000000
```

equals:

```text
192
```

Therefore:

```text
255.255.255.192
```

---

# 14. CIDR Subnetting Inside a /24

Suppose we have:

```text
192.168.1.0/24
```

and divide it into `/26`.

Originally:

```text
/24
```

We borrow:

```text
2 bits
```

because:

```text
26 - 24 = 2
```

Number of subnets:

```text
2² = 4
```

Therefore:

```text
192.168.1.0/26
192.168.1.64/26
192.168.1.128/26
192.168.1.192/26
```

---

# 15. VAPT Relevance

CIDR is extremely important during network penetration testing.

Suppose a client gives:

```text
10.10.20.0/24
```

You immediately know the assessment scope contains a network block of:

```text
256 IPv4 addresses
```

You can reason about:

```text
Network boundary
Host range
Broadcast address
Potential attack surface
Segmentation
Routing
Scope
```

---

# 16. CIDR and Attack Surface

Imagine:

```text
10.10.10.0/24
```

A network may contain:

```text
Web Server
Database Server
File Server
Developer Workstations
Printers
Network Devices
```

A pentester can think:

```text
CIDR
  ↓
Identify network scope
  ↓
Discover hosts
  ↓
Identify open ports
  ↓
Identify services
  ↓
Enumerate attack surface
```

**Only scan networks you are authorized to test.**

---

# 17. CIDR in Nmap

For an authorized lab network:

```bash
nmap 192.168.1.0/24
```

This tells Nmap to consider the CIDR block:

```text
192.168.1.0 → 192.168.1.255
```

For service detection:

```bash
nmap -sV 192.168.1.0/24
```

### Important

CIDR tells Nmap **which addresses belong to the target range**.

It does not itself determine which ports are open.

---

# 18. CIDR in Routing

Routers use prefixes to decide where traffic should go.

Example:

```text
10.10.0.0/16
```

means:

```text
10.10.x.x
```

belongs to that network prefix.

A routing table may contain:

```text
10.10.0.0/16 → Router A
192.168.1.0/24 → Router B
0.0.0.0/0 → Default Gateway
```

---

# 19. Longest Prefix Match

This is an important intermediate/advanced networking concept.

Suppose a router has:

```text
10.0.0.0/8
10.10.0.0/16
10.10.10.0/24
```

Destination:

```text
10.10.10.50
```

All three routes could match.

The router chooses:

```text
10.10.10.0/24
```

because it is the **most specific / longest prefix match**.

Mental model:

```text
More /bits
    ↓
More specific
    ↓
Higher priority match
```

---

# 20. Route Aggregation

CIDR also helps reduce routing-table size.

Instead of advertising many individual networks:

```text
10.10.0.0/24
10.10.1.0/24
10.10.2.0/24
10.10.3.0/24
```

they may potentially be summarized as:

```text
10.10.0.0/22
```

provided the address boundaries and network structure allow that aggregation.

This is called:

**Route summarization / route aggregation.**

---

# 21. Public vs Private CIDR

Common private IPv4 ranges:

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

These are defined by RFC 1918.

Example:

```text
192.168.1.0/24
```

is a private network.

It is commonly used inside:

* Home networks
* Offices
* Labs
* Cloud VPC/VNet environments

---

# 22. CIDR and NAT

Typical home network:

```text
             Internet
                 |
          Public IP
                 |
               NAT
                 |
        192.168.1.0/24
        /       |       \
     PC       Phone     Laptop
```

The internal devices use private addresses.

NAT allows many internal devices to share a public IPv4 address.

---

# 23. CIDR and Network Segmentation

Organizations can divide networks:

```text
10.10.10.0/24 → Users
10.10.20.0/24 → Servers
10.10.30.0/24 → Security/Management
10.10.40.0/24 → Guest
```

This can help with:

* Access control
* Firewall policies
* Isolation
* Monitoring
* Limiting lateral movement

### VAPT Perspective

During an authorized assessment, you may test whether segmentation actually prevents unauthorized communication between these zones.

---

# 24. CIDR vs Subnet Mask

They represent the same network boundary in different notation.

Example:

```text
192.168.1.0/24
```

CIDR notation.

Equivalent:

```text
192.168.1.0
255.255.255.0
```

Subnet-mask notation.

---

# 25. CIDR vs IP Address

These are **not the same thing**.

```text
192.168.1.25
```

= IP address

```text
192.168.1.0/24
```

= network expressed with CIDR

A host can be:

```text
192.168.1.25/24
```

where:

```text
192.168.1.25 → host IP
/24           → network prefix
```

---

# 26. Common CIDR Mistakes

### ❌ Mistake 1

Thinking `/24` means 24 hosts.

Wrong.

It means:

```text
24 network bits
```

---

### ❌ Mistake 2

Thinking `/30` has 30 hosts.

Wrong.

It has:

```text
32 - 30 = 2 host bits
```

Therefore:

```text
2² = 4 total addresses
2 traditional usable hosts
```

---

### ❌ Mistake 3

Always subtracting 2.

Remember:

```text
/31 and /32 have special/common uses.
```

---

### ❌ Mistake 4

Confusing network and host.

Example:

```text
192.168.1.0/24
```

`192.168.1.0` is the network address, not normally a host address in that subnet.

---

### ❌ Mistake 5

Thinking CIDR is only for subnetting.

CIDR is also important for:

* Routing
* Route aggregation
* ACLs
* Firewalls
* Cloud networking
* Network scanning
* VAPT scope

---

# 27. Windows Practical Commands

### Check IP configuration

```powershell
ipconfig
```

Detailed information:

```powershell
ipconfig /all
```

### Check routing table

```powershell
route print
```

---

# 28. Linux/Kali Practical Commands

### View addresses

```bash
ip addr
```

or:

```bash
ip a
```

### View routes

```bash
ip route
```

### Test a local lab subnet

```bash
ip route
```

Example output may contain:

```text
192.168.1.0/24 dev eth0
```

This tells you that the interface has a route to the `192.168.1.0/24` network.

---

# 29. Python — CIDR Calculation

Python has a useful standard-library module:

```python
import ipaddress

network = ipaddress.ip_network("192.168.1.0/26")

print("Network:", network.network_address)
print("Broadcast:", network.broadcast_address)
print("Prefix:", network.prefixlen)
print("Total:", network.num_addresses)
```

For a host:

```python
import ipaddress

ip = ipaddress.ip_interface("192.168.1.70/26")

print("IP:", ip.ip)
print("Network:", ip.network)
```

This is useful when building networking/recon automation tools.

---

# 30. Practice Example

Find:

```text
172.16.50.130/27
```

### Step 1

Host bits:

```text
32 - 27 = 5
```

### Step 2

Total:

```text
2⁵ = 32
```

### Step 3

Mask:

```text
/27 = 255.255.255.224
```

### Step 4

Block size:

```text
256 - 224 = 32
```

Network boundaries:

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

`130` belongs to:

```text
128–159
```

Therefore:

```text
Network:
172.16.50.128

Broadcast:
172.16.50.159

Traditional usable:
172.16.50.129
-
172.16.50.158
```

---

# 31. 🔥 Must-Memorize CIDR Table

```text
/24 → 256 total → 254 traditional usable
/25 → 128 total → 126 traditional usable
/26 → 64 total  → 62 traditional usable
/27 → 32 total  → 30 traditional usable
/28 → 16 total  → 14 traditional usable
/29 → 8 total   → 6 traditional usable
/30 → 4 total   → 2 traditional usable
/31 → 2 addresses → point-to-point use
/32 → 1 address → host/route
```

### Prefix Memory

```text
/24 = 255.255.255.0
/25 = 255.255.255.128
/26 = 255.255.255.192
/27 = 255.255.255.224
/28 = 255.255.255.240
/29 = 255.255.255.248
/30 = 255.255.255.252
```

---

# 32. 🧠 Mental Model

Whenever you see:

```text
192.168.10.50/27
```

Think:

```text
IPv4
 ↓
32 bits
 ↓
/27 = 27 network bits
 ↓
5 host bits
 ↓
2⁵ = 32 addresses
 ↓
Mask = 255.255.255.224
 ↓
Block size = 32
 ↓
Find network boundary
 ↓
Find broadcast
 ↓
Find host range
```

That's the complete CIDR thought process.

---

# 33. 🎯 Interview Questions

### Q1. What does `/24` mean?

**Answer:** 24 network bits and 8 host bits.

### Q2. How many total addresses exist in `/26`?

**Answer:** 64.

### Q3. How many traditional usable host addresses exist in `/26`?

**Answer:** 62.

### Q4. What is `/24` in subnet-mask notation?

**Answer:**

```text
255.255.255.0
```

### Q5. What is CIDR?

**Answer:** Classless Inter-Domain Routing, a method of representing IP networks using a prefix length.

### Q6. What is longest prefix match?

**Answer:** Routers select the most specific matching route.

### Q7. What does `/32` represent?

**Answer:** A single IPv4 address/host route.

### Q8. Why is CIDR important in VAPT?

**Answer:** It helps understand authorized network scope, segmentation, routing, and potential attack surface.

---

# 34. 🧪 Practical Lab

Use only your own machine, VM, or an explicitly authorized lab.

### Lab 1 — CIDR Calculator

Use Python:

```python
import ipaddress

net = ipaddress.ip_network("192.168.10.0/27")

print("Network:", net.network_address)
print("Broadcast:", net.broadcast_address)
print("Total:", net.num_addresses)
```

Try:

```text
10.0.0.0/24
192.168.1.0/26
172.16.10.0/28
```

---

### Lab 2 — Find Network Information

Calculate manually:

```text
192.168.1.67/26
10.10.10.130/27
172.16.5.200/28
```

For each find:

```text
Subnet Mask
Network Address
Broadcast Address
Host Range
Total Addresses
Traditional Usable Hosts
```

---

### Lab 3 — VAPT Scope Thinking

Imagine your authorized lab scope is:

```text
192.168.56.0/24
```

Before scanning, determine:

```text
Network address
Broadcast address
Total address space
Traditional usable host range
```

Then understand how a tool such as Nmap interprets the CIDR range.

---

# 35. 📝 MCQs

### 1. What does `/24` represent?

A. 24 hosts
B. 24 network bits
C. 24 IP addresses
D. 24 bytes

**Answer: B**

---

### 2. How many total IPv4 addresses are in `/26`?

A. 32
B. 64
C. 128
D. 256

**Answer: B**

---

### 3. `/26` corresponds to:

A. `255.255.255.0`
B. `255.255.255.128`
C. `255.255.255.192`
D. `255.255.255.224`

**Answer: C**

---

### 4. How many host bits exist in `/28`?

A. 2
B. 4
C. 8
D. 28

**Answer: B**

---

### 5. What is the traditional usable host count of `/28`?

A. 16
B. 15
C. 14
D. 12

**Answer: C**

---

### 6. Which is a private IPv4 CIDR block?

A. `8.8.8.0/24`
B. `10.0.0.0/8`
C. `1.1.1.0/24`
D. `172.0.0.0/8`

**Answer: B**

---

### 7. What does `/32` commonly represent?

A. 32 hosts
B. A single IPv4 address
C. 32 networks
D. A Class C network

**Answer: B**

---

### 8. What happens to the number of addresses when prefix length increases by one?

A. Doubles
B. Halves
C. Stays same
D. Becomes zero

**Answer: B**

---

### 9. Which concept is associated with routers selecting the most specific route?

A. NAT
B. DNS
C. Longest Prefix Match
D. DHCP

**Answer: C**

---

### 10. What is the traditional usable host count for `/30`?

A. 2
B. 4
C. 30
D. 32

**Answer: A**

---

# 36. ⭐ Key Takeaways

You should now understand:

* CIDR = **Classless Inter-Domain Routing**
* IPv4 = **32 bits**
* `/N` = number of network/prefix bits
* Host bits = `32 - prefix`
* Total addresses = `2^host_bits`
* Traditional usable hosts = `2^host_bits - 2`
* `/31` and `/32` are special cases
* CIDR determines network boundaries
* CIDR is used in routing and route aggregation
* CIDR is important for VAPT scope and network enumeration
* Private IPv4 ranges use CIDR too
* Routers use **longest prefix match**
* Nmap can accept CIDR ranges for authorized scanning

---

# 🔥 Final Mental Model

```text
                CIDR
                  |
        ┌─────────┴─────────┐
        ↓                   ↓
   Network Prefix       Host Portion
        |                   |
      /24                 8 bits
      /26                 6 bits
      /28                 4 bits
        |                   |
        └─────────┬─────────┘
                  ↓
            Network Range
                  ↓
        ┌─────────┴─────────┐
        ↓                   ↓
   Network Address       Broadcast
        ↓                   ↓
             Host Range
                  ↓
       Routing / Firewall
                  ↓
            VAPT Scope
                  ↓
       Host Discovery → Ports
                  ↓
              Services
                  ↓
            Attack Surface
```
