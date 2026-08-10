# IPv6 Basics and Security Implications

> **Goal:** Understand IPv6 addressing, notation, address types, Neighbor Discovery,
> routing, and the major security implications relevant to networking and VAPT.
>
> **Difficulty:** 🟢 Beginner → 🟡 Intermediate → 🔴 Security
>
> **Cybersecurity Relevance:** ⭐⭐⭐⭐⭐

---

# 1. What Is IPv6?

IPv6 stands for **Internet Protocol version 6**.

It was developed mainly because IPv4 has a limited number of addresses.

### IPv4

IPv4 uses:

```text

2^32 ≈ 4.29 billion addresses
```

---

### IPv6

IPv6 uses:

```text
128 bits
```

Example:

```text
2001:db8:1234:5678::10
```

IPv6 provides:

```text
2^128 addresses
```

This is an extremely large address space.

---

# 2. Why Was IPv6 Created?

Main reasons:

* IPv4 address exhaustion
* Growth of Internet-connected devices
* Better address allocation
* Improved support for autoconfiguration
* More efficient network architecture
* Modernized network-layer functionality

---

# 3. IPv4 vs IPv6

| Feature               | IPv4            | IPv6                                            |
| --------------------- | --------------- | ----------------------------------------------- |
| Address size          | 32-bit          | 128-bit                                         |
| Example               | `192.168.1.10`  | `2001:db8::10`                                  |
| Address notation      | Decimal         | Hexadecimal                                     |
| Broadcast             | Supported       | No traditional broadcast                        |
| Multicast             | Supported       | Supported                                       |
| Address configuration | Manual/DHCP     | SLAAC/DHCPv6/Manual                             |
| NAT                   | Very common     | Generally not required for address conservation |
| Neighbor discovery    | ARP             | NDP                                             |
| Header                | Variable length | Fixed base header                               |

---

# 4. IPv6 Address Structure

IPv6 addresses contain:

```text
128 bits
```

They are normally written as **8 groups of 16 bits**.

Example:

```text
2001:0db8:1234:5678:0000:0000:0000:0010
```

Each group is called a:

```text
Hextet
```

Each hextet contains:

```text
4 hexadecimal digits
```

---

# 5. Hexadecimal Basics

IPv6 uses hexadecimal.

Hexadecimal digits are:

```text
0 1 2 3 4 5 6 7 8 9 A B C D E F
```

Where:

```text
A = 10
B = 11
C = 12
D = 13
E = 14
F = 15
```

Example:

```text
FFFF
```

represents:

```text
16 bits
```

because each hexadecimal digit represents 4 bits.

```text
4 × 4 = 16 bits
```

---

# 6. IPv6 Address Compression

IPv6 addresses can become long.

For example:

```text
2001:0db8:0000:0000:0000:0000:0000:0010
```

We can remove leading zeros from each hextet:

```text
2001:db8:0:0:0:0:0:10
```

Then consecutive zero groups can be replaced by:

```text
::
```

Final:

```text
2001:db8::10
```

---

# 7. Important Compression Rule

`::` can represent one or more consecutive groups of zeros.

But:

> `::` should normally be used only once in an IPv6 address.

Example:

```text
2001:db8::10
```

Correct.

Avoid:

```text
2001::db8::10
```

because it becomes ambiguous.

---

# 8. Expanding an IPv6 Address

Example:

```text
2001:db8::10
```

There must be 8 total hextets.

Existing groups:

```text
2001
db8
10
```

So five groups of zeros are missing.

Expanded form:

```text
2001:0db8:0000:0000:0000:0000:0000:0010
```

---

# 9. IPv6 Prefix Length

IPv6 commonly uses CIDR notation.

Example:

```text
2001:db8:1234::/48
```

The:

```text
/48
```

means the first 48 bits are the network prefix.

Similar to IPv4:

```text
192.168.1.0/24
```

---

# 10. Common IPv6 Address Types

Important IPv6 address categories include:

1. Global Unicast
2. Link-Local
3. Unique Local
4. Multicast
5. Loopback
6. Unspecified

---

# 11. Global Unicast Address

Global Unicast addresses are publicly routable IPv6 addresses.

Common range:

```text
2000::/3
```

Example:

```text
2001:db8:1234::10
```

Note:

`2001:db8::/32` is specifically reserved for documentation/examples and should not be treated as a real public address.

---

# 12. Link-Local Address

Link-local IPv6 addresses begin with:

```text
FE80::/10
```

Example:

```text
fe80::1
```

They are used for communication on the local network segment.

Important:

> Link-local addresses are not normally routed across IPv6 routers.

---

# 13. Why Link-Local Addresses Matter in Security

Even when a device does not have a globally routable IPv6 address, it may still have a link-local IPv6 address.

Therefore, during an authorized internal assessment, IPv6 should not be ignored simply because the network primarily uses IPv4.

---

# 14. Unique Local Address (ULA)

ULA addresses are intended for private/internal IPv6 networks.

Range:

```text
fc00::/7
```

In practice, locally assigned ULA space commonly uses:

```text
fd00::/8
```

Example:

```text
fd12:3456:789a::10
```

Think of ULA as roughly comparable to private IPv4 addressing, although the mechanisms and design are different.

---

# 15. Loopback Address

IPv6 loopback:

```text
::1
```

IPv4 equivalent:

```text
127.0.0.1
```

It refers to the local machine.

Example:

```bash
ping -6 ::1
```

---

# 16. Unspecified Address

IPv6 unspecified address:

```text
::
```

IPv4 equivalent concept:

```text
0.0.0.0
```

It means:

> No specific address has been assigned/selected.

It is not a normal destination address for communication.

---

# 17. Multicast

IPv6 uses multicast extensively.

Multicast addresses begin with:

```text
FF00::/8
```

Example:

```text
ff02::1
```

This represents all IPv6 nodes on the local link.

---

# 18. IPv6 Does NOT Use Traditional Broadcast

IPv4 commonly uses broadcast.

IPv6 does not have traditional broadcast.

Instead, IPv6 relies heavily on:

```text
Multicast
```

This is an important difference.

---

# 19. IPv6 Address Summary

| Type           | Range/Address | Purpose                     |
| -------------- | ------------- | --------------------------- |
| Global Unicast | `2000::/3`    | Publicly routable           |
| Link-Local     | `fe80::/10`   | Local-link communication    |
| ULA            | `fc00::/7`    | Private/internal addressing |
| Multicast      | `ff00::/8`    | One-to-many communication   |
| Loopback       | `::1`         | Local host                  |
| Unspecified    | `::`          | No specific address         |

---

# 20. IPv6 Interface Configuration

On Linux/Kali:

```bash
ip -6 addr
```

or:

```bash
ip -6 address
```

To display IPv6 routes:

```bash
ip -6 route
```

To display IPv6 neighbors:

```bash
ip -6 neigh
```

---

# 21. Basic IPv6 Connectivity Test

IPv6 ping:

```bash
ping -6 2001:db8::10
```

For localhost:

```bash
ping -6 ::1
```

On some systems:

```bash
ping6 ::1
```

---

# 22. DNS and IPv6

IPv4 commonly uses:

```text
A record
```

IPv6 commonly uses:

```text
AAAA record
```

Example:

```text
example.com → A → IPv4
example.com → AAAA → IPv6
```

Check with:

```bash
dig AAAA example.com
```

or:

```bash
nslookup -type=AAAA example.com
```

---

# 23. Why AAAA Records Matter in VAPT

Suppose a website has:

```text
A     → IPv4
AAAA  → IPv6
```

A tester who checks only IPv4 may miss the IPv6 exposure.

Therefore:

```text
DNS Enumeration
        ↓
A + AAAA Records
        ↓
IPv4 + IPv6 Assessment
```

This is an important real-world security concept.

---

# 24. IPv6 and NDP

IPv6 does not use ARP.

Instead, IPv6 uses:

# Neighbor Discovery Protocol (NDP)

NDP is based on:

```text
ICMPv6
```

It performs functions such as:

* Neighbor discovery
* Router discovery
* Address resolution
* Duplicate Address Detection
* Router information discovery

---

# 25. IPv6 vs ARP

| IPv4                    | IPv6                          |
| ----------------------- | ----------------------------- |
| ARP                     | NDP                           |
| ARP requests            | ICMPv6 Neighbor Solicitation  |
| ARP replies             | ICMPv6 Neighbor Advertisement |
| Broadcast commonly used | Multicast heavily used        |

---

# 26. Important NDP Messages

NDP includes several ICMPv6 message types.

Important ones:

### Neighbor Solicitation (NS)

Used for:

* Neighbor discovery
* Address resolution
* Duplicate Address Detection

### Neighbor Advertisement (NA)

Response associated with Neighbor Solicitation.

### Router Solicitation (RS)

Host asks routers for information.

### Router Advertisement (RA)

Router provides network configuration information.

---

# 27. Router Advertisement

Router Advertisements can communicate information such as:

* Network prefix
* Default router information
* Autoconfiguration parameters

This is important for security because hosts may automatically configure IPv6 networking based on router advertisements.

---

# 28. SLAAC

SLAAC means:

# Stateless Address Autoconfiguration

It allows IPv6 hosts to configure addresses automatically.

Basic idea:

```text
Router Advertisement
        ↓
Network Prefix
        ↓
Host creates IPv6 address
```

This reduces the need for manual configuration.

---

# 29. DHCPv6

IPv6 can also use:

```text
DHCPv6
```

Depending on the network design.

Important:

> SLAAC and DHCPv6 are different mechanisms and may be used in different combinations.

---

# 30. IPv6 Security Reality

IPv6 itself is not automatically:

```text
More secure
```

or:

```text
Less secure
```

Security depends heavily on:

* Configuration
* Firewall rules
* Routing
* Network architecture
* Monitoring
* Host security
* NDP controls
* Access controls

---

# 31. Major IPv6 Security Concern

One common problem is:

# IPv6 enabled but IPv6 security controls are ignored.

Example:

A company properly filters IPv4 traffic:

```text
Internet
   ↓
IPv4 Firewall
   ↓
Internal Network
```

But IPv6 is enabled on hosts without equivalent filtering:

```text
IPv6 Traffic
     ↓
Weak/Incorrect Filtering
     ↓
Internal Host
```

This creates a potential security gap.

---

# 32. Dual-Stack Networks

Many organizations use:

```text
IPv4 + IPv6
```

simultaneously.

This is called:

# Dual Stack

Example:

```text
Host
 ├── IPv4
 └── IPv6
```

Security teams must consider both protocols.

---

# 33. IPv6 Attack Surface

During an authorized assessment, consider:

* IPv6 interfaces
* IPv6 routing
* AAAA records
* IPv6-enabled services
* Firewall rules
* NDP behavior
* Router advertisements
* DNS configuration
* Dual-stack inconsistencies
* IPv6-only services

---

# 34. IPv6 Firewall Misconfiguration

Example:

IPv4 firewall:

```text
Allow: TCP 443
Block: Everything else
```

But IPv6 firewall:

```text
Poorly configured
```

A service could unintentionally become reachable over IPv6.

This is why:

> IPv4 security ≠ IPv6 security.

Both need to be tested independently.

---

# 35. NDP Security Risks

Because NDP is important to IPv6 networking, poorly controlled NDP can create risks.

Potential security concerns include:

* Rogue Router Advertisements
* Neighbor spoofing
* Incorrect network configuration
* Traffic interception opportunities
* Denial-of-service conditions

Testing these areas should only be performed in an authorized lab or engagement.

---

# 36. Rogue Router Advertisement Concept

Normal:

```text
Legitimate Router
       ↓
Router Advertisement
       ↓
Hosts configure network
```

Potentially dangerous:

```text
Unauthorized Device
       ↓
Fake Router Advertisement
       ↓
Hosts accept incorrect information
```

This is why IPv6 network controls need to be properly configured.

---

# 37. IPv6 Privacy Addresses

IPv6 hosts can use temporary/privacy addresses.

Why?

To reduce long-term tracking based on a stable interface identifier.

Depending on the operating system and configuration, a host may have:

```text
Stable address
+
Temporary address
```

This can make asset identification more complicated.

---

# 38. IPv6 Scanning Considerations

IPv6 scanning is different from IPv4 scanning.

IPv4 networks often have relatively small address ranges such as:

```text
192.168.1.0/24
```

An IPv6 subnet can be enormous.

For example:

```text
2001:db8:1234::/64
```

contains:

```text
2^64 addresses
```

Therefore:

> Blindly scanning every possible IPv6 address is generally impractical.

Pentesters instead rely heavily on:

* DNS
* Known addresses
* Asset inventories
* Routing information
* Neighbor information
* Application discovery
* Logs
* Cloud/network documentation

---

# 39. IPv6 Recon Workflow

Authorized assessment:

```text
1. Identify IPv6 ranges
        ↓
2. Check DNS AAAA records
        ↓
3. Identify IPv6-enabled hosts
        ↓
4. Check IPv6 routes
        ↓
5. Identify exposed services
        ↓
6. Compare IPv4 vs IPv6 exposure
        ↓
7. Review firewall controls
        ↓
8. Document findings
```

---

# 40. IPv4 vs IPv6 Attack Surface

A useful assessment table:

| Area               | IPv4         | IPv6                                         |
| ------------------ | ------------ | -------------------------------------------- |
| Address discovery  | Often easier | Different strategy                           |
| DNS                | A            | AAAA                                         |
| Neighbor discovery | ARP          | NDP                                          |
| Broadcast          | Yes          | No traditional broadcast                     |
| Multicast          | Yes          | Very important                               |
| NAT                | Common       | Usually unnecessary for address conservation |
| Firewall           | IPv4 rules   | Separate IPv6 rules may be required          |
| Routing            | IPv4         | IPv6                                         |
| Auto configuration | DHCP/manual  | SLAAC/DHCPv6/manual                          |

---

# 41. Useful Kali Commands

## Show IPv6 addresses

```bash
ip -6 addr
```

## Show IPv6 routes

```bash
ip -6 route
```

## Show IPv6 neighbors

```bash
ip -6 neigh
```

## Test IPv6 connectivity

```bash
ping -6 ::1
```

## DNS AAAA lookup

```bash
dig AAAA example.com
```

## Nmap IPv6 scanning

For an authorized target:

```bash
nmap -6 <IPv6-address>
```

Service detection:

```bash
nmap -6 -sV <IPv6-address>
```

---

# 42. Important Nmap IPv6 Concept

The:

```text
-6
```

option tells Nmap to use IPv6.

Example:

```bash
nmap -6 -sV 2001:db8::10
```

This is useful when the target actually supports IPv6.

---

# 43. Python and IPv6

Python's `socket` library supports IPv6.

IPv4:

```python
socket.AF_INET
```

IPv6:

```python
socket.AF_INET6
```

Example:

```python
import socket

target = "::1"
port = 80

sock = socket.socket(
    socket.AF_INET6,
    socket.SOCK_STREAM
)

sock.settimeout(3)

try:
    sock.connect((target, port))
    print("[+] IPv6 connection successful")
except Exception as e:
    print("[-] Connection failed:", e)
finally:
    sock.close()
```

Use this only against systems you own or are explicitly authorized to test.

---

# 44. Python: Resolving IPv4 and IPv6

Python can resolve multiple address families.

```python
import socket

target = "example.com"

results = socket.getaddrinfo(
    target,
    80,
    socket.AF_UNSPEC,
    socket.SOCK_STREAM
)

for result in results:
    family = result[0]
    address = result[4]

    print(f"Family: {family}")
    print(f"Address: {address}")
```

This is useful for understanding:

```text
IPv4 + IPv6
```

availability.

---

# 45. Security Assessment: IPv6 Checklist

During an authorized VAPT:

### Discovery

* [ ] Identify IPv6-enabled hosts
* [ ] Check AAAA DNS records
* [ ] Identify IPv6 ranges
* [ ] Review routing
* [ ] Review interfaces

### Services

* [ ] Check exposed ports
* [ ] Identify services
* [ ] Compare IPv4 and IPv6 exposure

### Security Controls

* [ ] Check IPv6 firewall rules
* [ ] Check NDP protections
* [ ] Review router advertisements
* [ ] Check ACLs
* [ ] Review monitoring

### Configuration

* [ ] Check SLAAC configuration
* [ ] Check DHCPv6 where applicable
* [ ] Check DNS configuration
* [ ] Check dual-stack consistency

---

# 46. Common Beginner Mistakes

## Mistake 1

Thinking:

```text
IPv4 firewall = IPv6 firewall
```

Wrong.

IPv6 traffic needs appropriate IPv6 filtering.

---

## Mistake 2

Ignoring AAAA records.

Always consider:

```text
A
AAAA
```

---

## Mistake 3

Thinking IPv6 uses ARP.

It doesn't.

IPv6 uses:

```text
NDP
```

---

## Mistake 4

Thinking IPv6 has broadcast.

IPv6 does not use traditional broadcast.

---

## Mistake 5

Trying to scan an entire /64 blindly.

A /64 has:

```text
2^64
```

possible interface addresses.

Use intelligent asset discovery instead.

---

# 47. Interview Questions

## Q1. How many bits are in IPv6?

**Answer:**

128 bits.

---

## Q2. What is the IPv6 loopback address?

**Answer:**

```text
::1
```

---

## Q3. What is the IPv6 equivalent of ARP?

**Answer:**

Neighbor Discovery Protocol (NDP).

---

## Q4. Which DNS record maps a domain to IPv6?

**Answer:**

AAAA.

---

## Q5. Does IPv6 use broadcast?

**Answer:**

No traditional broadcast; IPv6 uses multicast extensively.

---

## Q6. What is a link-local IPv6 address?

**Answer:**

An address used for communication on the local network link.

Common range:

```text
fe80::/10
```

---

## Q7. What is SLAAC?

**Answer:**

Stateless Address Autoconfiguration, which allows IPv6 hosts to configure addresses using information learned from routers.

---

## Q8. What is dual-stack?

**Answer:**

Running IPv4 and IPv6 simultaneously on a host/network.

---

## Q9. What does Nmap `-6` do?

**Answer:**

Performs IPv6 scanning.

---

## Q10. Why is IPv6 important in VAPT?

**Answer:**

Because IPv6 can create a separate attack surface. If IPv6 services or security controls are overlooked, vulnerabilities may remain exposed even when IPv4 is properly secured.

---

# 48. Quick Memory Sheet

```text
IPv6
 ↓
128-bit
 ↓
Hexadecimal
 ↓
8 Hextets
 ↓
CIDR prefix
```

Important addresses:

```text
::1          → Loopback
::           → Unspecified
fe80::/10    → Link-local
fc00::/7     → ULA
ff00::/8     → Multicast
2000::/3     → Global Unicast
```

Important protocols:

```text
IPv4 → ARP
IPv6 → NDP
```

Important DNS:

```text
IPv4 → A
IPv6 → AAAA
```

Important Nmap:

```text
-6 → IPv6
-sV → Service detection
```

---

# 49. Practical Lab

## Lab Goal

Understand IPv6 on your own Kali machine.

### Step 1 — View IPv6 addresses

```bash
ip -6 addr
```

Find:

```text
fe80::
```

---

### Step 2 — Check loopback

```bash
ping -6 ::1
```

Expected:

```text
Successful replies
```

---

### Step 3 — View IPv6 routing

```bash
ip -6 route
```

Understand:

```text
Destination
Gateway
Interface
```

---

### Step 4 — View neighbors

```bash
ip -6 neigh
```

Observe how IPv6 maintains neighbor information.

---

### Step 5 — Check AAAA record

```bash
dig AAAA example.com
```

Look for:

```text
AAAA
```

---

### Step 6 — Check IPv6 with Nmap

Only use an authorized target:

```bash
nmap -6 -sV <IPv6-address>
```

---

# 50. Mini Project — IPv4 vs IPv6 Exposure Checker

Build a small Python tool that:

1. Accepts a domain
2. Resolves A records
3. Resolves AAAA records
4. Prints IPv4 addresses
5. Prints IPv6 addresses
6. Reports whether IPv6 is enabled

Concept:

```text
Domain
  ↓
DNS Resolution
  ↓
A Records ───→ IPv4
  ↓
AAAA Records ─→ IPv6
  ↓
Compare Attack Surface
```

This is an excellent beginner Python + networking project.

---

# 51. What You Should Actually Understand

Don't only memorize:

```text
fe80
::1
AAAA
NDP
```

Understand the complete relationship:

```text
IPv6 Address
      ↓
Prefix
      ↓
Interface
      ↓
Neighbor Discovery
      ↓
Router Discovery
      ↓
Routing
      ↓
Firewall
      ↓
Service Exposure
      ↓
Security Assessment
```

This is much more valuable than memorizing definitions.

---

# 52. Final VAPT Mental Model

When you see a target:

```text
example.com
```

Don't think only:

```text
IPv4?
```

Think:

```text
DNS
 ├── A
 └── AAAA
       ↓
IPv4 + IPv6
       ↓
Services
       ↓
Firewall
       ↓
Routing
       ↓
NDP
       ↓
Attack Surface
```

---

# 53. Key Takeaways

* IPv6 uses 128-bit addresses.
* IPv6 addresses are written in hexadecimal.
* IPv6 uses CIDR prefixes.
* `::1` is loopback.
* `fe80::/10` is link-local.
* `fc00::/7` is ULA space.
* `ff00::/8` is multicast.
* IPv6 does not use traditional broadcast.
* IPv6 uses NDP instead of ARP.
* AAAA records are used for IPv6 DNS resolution.
* SLAAC enables automatic IPv6 address configuration.
* IPv6 can introduce a separate attack surface.
* IPv4 and IPv6 firewall policies must both be considered.
* Dual-stack environments require testing of both protocols.
* Blind IPv6 scanning is generally impractical.
* In VAPT, IPv6 should be treated as a first-class part of the attack surface.

---

```
```
