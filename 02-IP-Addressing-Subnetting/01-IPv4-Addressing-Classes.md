# IPv4 Addressing & Classes — Complete Notes

> **Goal:** Understand IPv4 from zero → networking → VAPT practical use.

---

# 1. What is an IP Address?

An **IP (Internet Protocol) address** is a logical address given to a device on a network.

It helps devices identify:

* **Who** the device is
* **Where** the device is on the network
* Where network traffic should be sent

### Simple Example

```text
Laptop → 192.168.1.10
Router → 192.168.1.1
Phone  → 192.168.1.20
```

Think of an IP address like a **house address**.

```text
House Address → Finds a house
IP Address    → Finds a network device
```

---

# 2. What is IPv4?

IPv4 stands for:

> **Internet Protocol Version 4**

IPv4 uses **32 bits**.

These 32 bits are divided into **4 groups of 8 bits**.

```text
32 bits

8 bits    8 bits    8 bits    8 bits
  ↓         ↓         ↓         ↓
192       .168       .1        .10
```

Each 8-bit group is called an **octet**.

Therefore:

```text
IPv4 = 4 octets
```

---

# 3. IPv4 Format

An IPv4 address looks like:

```text
192.168.1.10
```

Each octet can contain a value from:

```text
0 → 255
```

So:

```text
Valid:
192.168.1.10
10.0.0.1
172.16.5.20

Invalid:
256.10.1.1
192.168.1.300
```

Why can't an octet exceed 255?

Because one octet contains 8 bits.

```text
2⁸ = 256 values
```

The values are:

```text
0–255
```

---

# 4. IPv4 Binary Representation

Every IPv4 address can be represented in binary.

Example:

```text
192.168.1.10
```

becomes:

```text
11000000.10101000.00000001.00001010
```

Each octet contains 8 bits.

### Binary Place Values

```text
128 64 32 16 8 4 2 1
```

For example:

```text
192

128 + 64 = 192

Therefore:

192 = 11000000
```

Another example:

```text
10

8 + 2 = 10

Therefore:

10 = 00001010
```

---

# 5. IPv4 Has Two Main Parts

An IPv4 address generally contains:

```text
Network Portion + Host Portion
```

Example:

```text
192.168.1.10
```

Depending on the subnet mask, part of the address identifies the network and the remaining part identifies the host.

Example:

```text
Network      Host
192.168.1    .10
```

⚠️ Important:

The exact network/host boundary depends on the **subnet mask/CIDR**.

For example:

```text
192.168.1.10/24
```

has:

```text
Network = 192.168.1.0
Host    = 10
```

You will study this deeply in:

```text
02-CIDR-Notation-Deep-Dive.md
03-Subnetting-Step-by-Step.md
```

---

# 6. What are IPv4 Classes?

Historically, IPv4 addresses were divided into:

```text
Class A
Class B
Class C
Class D
Class E
```

This system is called **classful addressing**.

Today, modern networks mainly use **CIDR** instead of traditional classes.

However, understanding classes is still important for:

* Networking fundamentals
* Exams
* Interviews
* Understanding older network designs
* Understanding private IP ranges
* Understanding subnetting history

---

# 7. IPv4 Class A

### Range

```text
1.0.0.0 → 126.255.255.255
```

### Default Mask

```text
255.0.0.0
```

or:

```text
/8
```

### Structure

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

### Number of Hosts

A /8 network has:

```text
2²⁴ addresses
```

Traditional usable hosts:

```text
2²⁴ - 2
= 16,777,214
```

The `-2` accounts for:

* Network address
* Broadcast address

---

# 8. Class B

### Range

```text
128.0.0.0 → 191.255.255.255
```

### Default Mask

```text
255.255.0.0
```

or:

```text
/16
```

### Structure

```text
Network.Network.Host.Host
```

Example:

```text
172.20.10.50
```

Historically:

```text
Network = 172.20
Host    = 10.50
```

### Hosts

A /16 network contains:

```text
2¹⁶ addresses
```

Traditional usable hosts:

```text
2¹⁶ - 2
= 65,534
```

---

# 9. Class C

### Range

```text
192.0.0.0 → 223.255.255.255
```

### Default Mask

```text
255.255.255.0
```

or:

```text
/24
```

### Structure

```text
Network.Network.Network.Host
```

Example:

```text
192.168.1.50
```

Historically:

```text
Network = 192.168.1
Host    = 50
```

### Hosts

A /24 network contains:

```text
2⁸ = 256 addresses
```

Traditional usable hosts:

```text
256 - 2 = 254
```

---

# 10. Class D

Class D was designed for **multicast**.

### Range

```text
224.0.0.0 → 239.255.255.255
```

Class D does **not** use the normal network/host model used by Class A/B/C.

Used for:

* Multicast traffic
* Streaming
* Routing protocols
* Group communication

Example:

```text
224.0.0.1
```

---

# 11. Class E

Class E was reserved for experimental/future purposes.

### Range

```text
240.0.0.0 → 255.255.255.255
```

It is not normally used for ordinary host addressing.

---

# 12. Class Summary

| Class | First Octet | Default Mask  | Default CIDR | General Purpose       |
| ----- | ----------: | ------------- | ------------ | --------------------- |
| A     |       1–126 | 255.0.0.0     | /8           | Very large networks   |
| B     |     128–191 | 255.255.0.0   | /16          | Medium/large networks |
| C     |     192–223 | 255.255.255.0 | /24          | Smaller networks      |
| D     |     224–239 | N/A           | N/A          | Multicast             |
| E     |     240–255 | N/A           | N/A          | Experimental/reserved |

⚠️ `127.x.x.x` is reserved for **loopback**, so it is not treated as a normal Class A host range.

---

# 13. Private IPv4 Addresses

Private IP addresses are used inside private networks.

The three major private IPv4 ranges are:

### Private Class A Range

```text
10.0.0.0/8
```

Range:

```text
10.0.0.0 → 10.255.255.255
```

### Private Class B Range

```text
172.16.0.0/12
```

Range:

```text
172.16.0.0 → 172.31.255.255
```

### Private Class C Range

```text
192.168.0.0/16
```

Range:

```text
192.168.0.0 → 192.168.255.255
```

---

# 14. Private vs Public IP

## Private IP

Used inside private networks.

Example:

```text
192.168.1.10
```

Normally not directly routable across the public Internet.

## Public IP

Used for Internet communication.

Example:

```text
8.8.8.8
```

Public addresses are globally routable, subject to routing and filtering.

---

# 15. Loopback Address

The most famous loopback address is:

```text
127.0.0.1
```

It means:

> "This computer itself."

You may also see:

```text
localhost
```

Example:

```bash
ping 127.0.0.1
```

This tests the local TCP/IP stack rather than another machine.

### VAPT Relevance

When testing a locally running application:

```text
http://127.0.0.1:8000
```

you are connecting to a service on your own machine.

---

# 16. Special IPv4 Addresses You Should Know

| Address/Range     | Meaning                                               |
| ----------------- | ----------------------------------------------------- |
| `0.0.0.0`         | Unspecified address / all interfaces in some contexts |
| `127.0.0.0/8`     | Loopback                                              |
| `169.254.0.0/16`  | IPv4 link-local/APIPA                                 |
| `10.0.0.0/8`      | Private                                               |
| `172.16.0.0/12`   | Private                                               |
| `192.168.0.0/16`  | Private                                               |
| `224.0.0.0/4`     | Multicast                                             |
| `255.255.255.255` | Limited broadcast                                     |

---

# 17. Network Address

The **network address** identifies the network itself.

Example:

```text
192.168.1.0/24
```

Network address:

```text
192.168.1.0
```

It normally isn't assigned to a normal host.

---

# 18. Broadcast Address

The broadcast address is used to send traffic to all hosts within an IPv4 subnet.

For:

```text
192.168.1.0/24
```

broadcast address:

```text
192.168.1.255
```

Traditional usable host range:

```text
192.168.1.1 → 192.168.1.254
```

---

# 19. Default Gateway

The default gateway is usually the router/interface that a host uses to reach networks outside its local subnet.

Example:

```text
PC
192.168.1.10
     |
     ↓
Router
192.168.1.1
     |
     ↓
Internet
```

The PC may have:

```text
IP:      192.168.1.10
Gateway: 192.168.1.1
```

---

# 20. IPv4 Address Example

Suppose:

```text
IP Address: 192.168.1.25
Mask:       255.255.255.0
```

This corresponds to:

```text
192.168.1.25/24
```

The network is:

```text
192.168.1.0
```

The host is:

```text
25
```

Broadcast:

```text
192.168.1.255
```

Traditional usable range:

```text
192.168.1.1 → 192.168.1.254
```

---

# 21. How to Identify the Class Quickly

Look at the **first octet**.

Example:

```text
10.5.5.5
```

First octet:

```text
10
```

10 is between 1–126.

Therefore:

```text
Class A
```

Another:

```text
172.16.10.5
```

First octet:

```text
172
```

172 is between 128–191.

Therefore historically:

```text
Class B
```

Another:

```text
192.168.1.20
```

First octet:

```text
192
```

Therefore:

```text
Class C
```

---

# 22. Important: Class ≠ CIDR

This is a very important modern networking concept.

Old classful thinking:

```text
Class A → /8
Class B → /16
Class C → /24
```

Modern networking:

```text
CIDR
```

allows much more flexible prefixes.

For example:

```text
192.168.1.0/26
```

This is still within the traditional Class C range, but it is **not a /24 network**.

So don't assume:

```text
192.x.x.x = automatically /24
```

In modern networking, always check the **prefix/CIDR**.

---

# 23. VAPT Importance

Why should a pentester understand IPv4?

Because reconnaissance starts with understanding the network.

A pentester may need to determine:

```text
Target
 ↓
IP address
 ↓
Network
 ↓
Subnet
 ↓
Hosts
 ↓
Open ports
 ↓
Services
 ↓
Potential vulnerabilities
```

Example:

```text
192.168.1.0/24
```

A tester with authorization may identify active hosts within that network and then assess approved services.

---

# 24. Useful Kali Commands

### Show IP addresses

```bash
ip addr
```

or:

```bash
ip a
```

### Show routing table

```bash
ip route
```

### Check connectivity

```bash
ping 192.168.1.1
```

### DNS resolution

```bash
nslookup example.com
```

or:

```bash
dig example.com
```

### Show hostname

```bash
hostname
```

### Show IP information

```bash
hostname -I
```

---

# 25. Windows Commands

### Show network configuration

```cmd
ipconfig
```

Detailed:

```cmd
ipconfig /all
```

### Test connectivity

```cmd
ping 192.168.1.1
```

### Show routing table

```cmd
route print
```

---

# 26. VAPT Example

Imagine your authorized lab network is:

```text
192.168.56.0/24
```

You discover:

```text
192.168.56.10
192.168.56.20
192.168.56.30
```

You can then perform authorized enumeration:

```text
Host Discovery
      ↓
Port Scanning
      ↓
Service Detection
      ↓
Version Detection
      ↓
Vulnerability Assessment
      ↓
Report
```

The important point:

> **An IP address identifies a network endpoint; it does not automatically tell you what service or vulnerability exists there.**

---

# 27. Common Beginner Mistakes

### Mistake 1

Thinking:

```text
192.168.x.x = always Class C /24
```

❌ Not necessarily.

CIDR determines the actual network boundary.

---

### Mistake 2

Thinking:

```text
127.0.0.1 = Internet IP
```

❌ No.

It is loopback.

---

### Mistake 3

Thinking:

```text
Private IP = insecure
```

❌ No.

Private/public describes addressing and routing, not security.

---

### Mistake 4

Thinking:

```text
Open IP = vulnerable
```

❌ No.

An IP being reachable does not mean it is vulnerable.

---

### Mistake 5

Memorizing classes without understanding binary.

Better:

```text
IPv4
 ↓
32 bits
 ↓
4 octets
 ↓
Network + Host
 ↓
Subnet mask/CIDR
```

---

# 28. Interview Questions

## Q1. What is IPv4?

IPv4 is Internet Protocol version 4 that uses 32-bit addresses to identify network interfaces/endpoints.

---

## Q2. How many bits are in IPv4?

```text
32 bits
```

---

## Q3. How many octets are in IPv4?

```text
4 octets
```

---

## Q4. What is the range of one IPv4 octet?

```text
0–255
```

---

## Q5. What is the default Class A mask?

```text
255.0.0.0
```

or:

```text
/8
```

---

## Q6. What is the default Class B mask?

```text
255.255.0.0
```

or:

```text
/16
```

---

## Q7. What is the default Class C mask?

```text
255.255.255.0
```

or:

```text
/24
```

---

## Q8. What is 127.0.0.1?

Loopback address.

---

## Q9. What are the private IPv4 ranges?

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

---

## Q10. What is the difference between public and private IP?

Private IPs are intended for internal/private networks, while public IPs are globally routable on the Internet.

---

## Q11. What is a network address?

An address representing the subnet itself.

Example:

```text
192.168.1.0/24
```

Network address:

```text
192.168.1.0
```

---

## Q12. What is a broadcast address?

An IPv4 address used to reach all hosts in a subnet.

Example:

```text
192.168.1.255
```

for a traditional `/24`.

---

## Q13. What is CIDR?

CIDR stands for:

> Classless Inter-Domain Routing

It uses a prefix length such as:

```text
192.168.1.0/24
```

to specify the network portion.

---

## Q14. Is classful addressing still the main method today?

No.

Modern networks primarily use CIDR/classless addressing.

---

# 29. Quick Quiz

### Q1. IPv4 uses how many bits?

A. 16
B. 24
C. 32
D. 64

**Answer: C**

---

### Q2. Which is a private IPv4 range?

A. `8.8.8.0/24`
B. `10.0.0.0/8`
C. `224.0.0.0/4`
D. `127.0.0.0/8`

**Answer: B**

---

### Q3. What is 127.0.0.1?

A. Gateway
B. Broadcast
C. Loopback
D. Multicast

**Answer: C**

---

### Q4. Class C historically uses which default mask?

A. `/8`
B. `/12`
C. `/16`
D. `/24`

**Answer: D**

---

### Q5. Which class is used for multicast?

A. A
B. B
C. C
D. D

**Answer: D**

---

### Q6. Which command shows Linux IP information?

A. `ip a`
B. `mkdir`
C. `chmod`
D. `ps`

**Answer: A**

---

### Q7. Which address is private?

A. `10.10.10.10`
B. `8.8.8.8`
C. `1.1.1.1`
D. `224.0.0.1`

**Answer: A**

---

# 30. Must-Memorize Sheet

```text
IPv4
↓
32 bits
↓
4 octets
↓
Each octet = 8 bits
↓
Each octet = 0–255
```

### Classes

```text
Class A → 1–126  → /8
Class B → 128–191 → /16
Class C → 192–223 → /24
Class D → 224–239 → Multicast
Class E → 240–255 → Experimental/Reserved
```

### Private Ranges

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

### Important Special Addresses

```text
127.0.0.1        → Loopback
0.0.0.0          → Unspecified / all interfaces in context
169.254.0.0/16   → Link-local
224.0.0.0/4      → Multicast
255.255.255.255  → Limited broadcast
```

---

# 31. Learning Path

This file gives you the foundation.

Next learn:

```text
IPv4 Addressing
      ↓
CIDR
      ↓
Subnet Mask
      ↓
Network Address
      ↓
Broadcast Address
      ↓
Usable Host Range
      ↓
Subnetting
      ↓
Routing
      ↓
Network Recon
```

The next important file is:

```text
02-CIDR-Notation-Deep-Dive.md
```

There you should learn **why `/24`, `/25`, `/26`, `/27`, etc. work**, instead of simply memorizing them.

---

# Final Takeaway

If you remember only these points, remember:

```text
IPv4 = 32 bits
4 octets = 8 bits each
Each octet = 0–255

Class A = 1–126
Class B = 128–191
Class C = 192–223
Class D = Multicast
Class E = Experimental/Reserved

Private:
10/8
172.16/12
192.168/16

127.0.0.1 = Loopback

Modern networks use CIDR,
not traditional classful addressing.
```

> **Pentester mindset:** Don't just ask "What is the IP?" Ask **"Which network is it in, what is the prefix, what hosts can exist there, how is traffic routed, and what services are exposed?"**
