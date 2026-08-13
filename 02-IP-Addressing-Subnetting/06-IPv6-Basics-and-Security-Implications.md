

# 1. What Is IPv6?

**IPv6 (Internet Protocol version 6)** is the successor to IPv4.

IPv4 uses **32-bit addresses**, while IPv6 uses **128-bit addresses**.

### IPv4

```text
192.168.1.10
```

32 bits.

### IPv6

```text
2001:db8:1234:5678:abcd:ef01:2345:6789
```

128 bits.

### Why was IPv6 introduced?

The main reason was the limited IPv4 address space.

IPv4 provides approximately:

```text
2^32 ≈ 4.29 billion addresses
```

IPv6 provides:

```text
2^128
```

possible addresses.

That is an enormously larger address space.

---

# 2. IPv4 vs IPv6

| Feature            | IPv4            | IPv6                      |
| ------------------ | --------------- | ------------------------- |
| Address size       | 32-bit          | 128-bit                   |
| Example            | `192.168.1.10`  | `2001:db8::10`            |
| Notation           | Decimal         | Hexadecimal               |
| Broadcast          | Supported       | No                        |
| Multicast          | Supported       | Supported                 |
| Address resolution | ARP             | Neighbor Discovery (NDP)  |
| Header             | Variable length | Fixed base header         |
| NAT                | Very common     | Generally less necessary  |
| Configuration      | DHCP/manual     | SLAAC/DHCPv6/manual       |
| Address exhaustion | Major issue     | Practically not a concern |

> **Must Remember:** IPv6 does **not** simply mean "IPv4 with longer addresses." Several protocols and mechanisms are different.

---

# 3. IPv6 Address Format

IPv6 addresses contain **8 groups**, called hextets.

Each hextet contains up to **4 hexadecimal digits**.

Example:

```text
2001:0db8:0000:0000:0000:ff00:0042:8329
```

There are 8 groups:

```text
2001
0db8
0000
0000
0000
ff00
0042
8329
```

Each group represents **16 bits**.

Therefore:

```text
8 × 16 = 128 bits
```

---

# 4. Hexadecimal Basics

IPv6 uses hexadecimal.

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
ABCD
```

is a hexadecimal value.

---

# 5. IPv6 Address Compression

IPv6 addresses often contain many zeros.

The address:

```text
2001:0db8:0000:0000:0000:ff00:0042:8329
```

can be shortened.

## Rule 1 — Remove leading zeros

Within each hextet, leading zeros can be removed.

```text
0db8 → db8
0042 → 42
```

So:

```text
2001:db8:0:0:0:ff00:42:8329
```

---

# 6. Double-Colon `::`

Consecutive zero hextets can be compressed using:

```text
::
```

Example:

```text
2001:db8:0:0:0:ff00:42:8329
```

becomes:

```text
2001:db8::ff00:42:8329
```

### Important Rule

`::` can normally appear **only once** in an IPv6 address.

Why?

Because the receiver must be able to determine how many zero groups were removed.

---

# 7. Expanding an IPv6 Address

Suppose:

```text
2001:db8::1
```

We need 8 hextets.

Currently:

```text
2001
db8
1
```

Three groups exist.

Therefore, five groups of zeros are inserted:

```text
2001:0db8:0000:0000:0000:0000:0000:0001
```

---

# 8. IPv6 Prefix Length

IPv6 commonly uses CIDR notation.

Example:

```text
2001:db8:1234::/64
```

`/64` means the first 64 bits are the network prefix.

Conceptually:

```text
|-------- 64 bits --------|-------- 64 bits --------|
|       Network Prefix    |      Interface ID       |
```

A `/64` is extremely common for IPv6 LANs.

---

# 9. IPv6 Address Types

IPv6 has several important address categories.

## 9.1 Global Unicast

Used for globally routable IPv6 communication.

Example:

```text
2001:db8::1
```

`2000::/3` is the commonly recognized global-unicast range.

---

# 10. Link-Local Addresses

Link-local IPv6 addresses begin with:

```text
fe80::
```

Example:

```text
fe80::1234:5678:abcd:ef01
```

They are used for communication on the local network segment.

### Important

Link-local addresses are **not globally routable**.

They are extremely important for IPv6 networking because many local IPv6 functions use them.

---

# 11. Unique Local Addresses (ULA)

ULA addresses are intended for private/internal IPv6 networks.

Range:

```text
fc00::/7
```

In practice, locally assigned ULAs generally use:

```text
fd00::/8
```

Example:

```text
fd12:3456:789a::1
```

Think of ULA as roughly comparable to the role private IPv4 addresses play, but the mechanisms and expectations are not identical.

---

# 12. Loopback Address

IPv6 loopback is:

```text
::1
```

Equivalent IPv4 address:

```text
127.0.0.1
```

Example:

```bash
ping -6 ::1
```

---

# 13. Unspecified Address

IPv6 unspecified address:

```text
::
```

Equivalent conceptually to IPv4:

```text
0.0.0.0
```

It means "no specific address."

It is **not** a normal destination address for communication.

---

# 14. Multicast

IPv6 multicast addresses begin with:

```text
ff00::/8
```

Example:

```text
ff02::1
```

Multicast allows communication to a group of hosts.

IPv6 relies heavily on multicast.

---

# 15. IPv6 Has No Broadcast

One important difference:

```text
IPv4 → Broadcast
IPv6 → No broadcast
```

IPv6 uses multicast instead.

This is important when analyzing network traffic and troubleshooting IPv6.

---

# 16. SLAAC

**SLAAC = Stateless Address Autoconfiguration**

It allows an IPv6 host to automatically configure an address using information advertised by routers.

Basic idea:

```text
Router
   |
   | Router Advertisement
   ↓
Host
   |
   ↓
Creates IPv6 address
```

SLAAC can reduce the need for manual configuration.

---

# 17. DHCPv6

IPv6 can also use **DHCPv6**.

Two common approaches are:

```text
SLAAC
DHCPv6
```

They can also be used together depending on the network design.

### Important

Do not think:

> IPv6 = SLAAC only.

IPv6 supports multiple address-configuration mechanisms.

---

# 18. ARP vs Neighbor Discovery

IPv4 commonly uses:

```text
ARP
```

IPv6 does not use ARP.

Instead, IPv6 uses:

```text
NDP — Neighbor Discovery Protocol
```

NDP operates using **ICMPv6**.

Conceptually:

```text
IPv4
Host → ARP → Find MAC address

IPv6
Host → NDP/ICMPv6 → Discover neighbor
```

---

# 19. Important ICMPv6 Messages

NDP uses several ICMPv6 message types.

Important ones:

```text
RS → Router Solicitation
RA → Router Advertisement
NS → Neighbor Solicitation
NA → Neighbor Advertisement
```

### Simple flow

```text
Host
 |
 | Router Solicitation
 ↓
Router
 |
 | Router Advertisement
 ↓
Host
```

---

# 20. Why NDP Matters in Security

NDP is critical to IPv6 operation.

Security issues can arise if attackers can manipulate local IPv6 discovery traffic.

Examples of concerns include:

* Rogue Router Advertisements
* Neighbor Discovery spoofing
* Improper IPv6 filtering
* Weak switch protections
* Unexpected IPv6 paths

Therefore, IPv6 security requires more than simply securing IPv4.

---

# 21. IPv6 Security: The "IPv6 Is Automatically Secure" Myth

A common misconception is:

> "IPv6 is secure by default."

This is misleading.

IPv6 has security-related capabilities and improved protocol design, but **deploying IPv6 does not automatically make a network secure**.

Security still depends on:

* Firewall rules
* Network segmentation
* Router configuration
* Switch security
* Endpoint security
* Monitoring
* Authentication
* Patch management
* Secure services

---

# 22. IPv6 and IPsec

IPv6 was designed with support for IPsec in mind.

IPsec can provide:

* Authentication
* Integrity
* Confidentiality

However:

> IPv6 does not mean that every packet is automatically encrypted.

IPsec still needs to be configured and used appropriately.

---

# 23. IPv6 Security Implication: Dual Stack

Many organizations operate:

```text
IPv4 + IPv6
```

This is called:

# Dual Stack

Example:

```text
                 Internet
                    |
             ┌──────┴──────┐
             ↓             ↓
           IPv4           IPv6
             ↓             ↓
          Firewall      Firewall
             ↓             ↓
              Internal Host
```

### Security risk

An organization may carefully configure IPv4 firewall rules but forget IPv6.

This can create an unexpected attack path.

---

# 24. VAPT Perspective — IPv6 Enumeration

During an **authorized** assessment, a pentester should determine whether IPv6 is enabled.

Questions include:

```text
Is IPv6 enabled?
↓
What IPv6 addresses exist?
↓
What routes exist?
↓
What services listen over IPv6?
↓
Are IPv6 firewall rules equivalent to IPv4?
↓
Is IPv6 traffic monitored?
```

This is important because:

> An IPv4-only assessment can miss IPv6 attack surface.

---

# 25. IPv6 Listening Services

A service might listen on:

```text
0.0.0.0
```

for IPv4 and/or:

```text
::
```

for IPv6.

For example, a server may expose:

```text
IPv4 → 0.0.0.0:443
IPv6 → [::]:443
```

A VAPT tester should verify that security controls apply to both protocol families.

---

# 26. Checking IPv6 on Linux

View interfaces:

```bash
ip -6 addr
```

Example:

```text
inet6 fe80::1234/64 scope link
inet6 2001:db8::10/64 scope global
```

View IPv6 routes:

```bash
ip -6 route
```

Check listening sockets:

```bash
ss -lnt
```

You may see IPv6 listeners such as:

```text
[::]:443
```

---

# 27. Windows IPv6 Commands

View IPv6 configuration:

```cmd
ipconfig
```

More detailed information:

```cmd
netsh interface ipv6 show addresses
```

View IPv6 routes:

```cmd
netsh interface ipv6 show route
```

PowerShell:

```powershell
Get-NetIPAddress -AddressFamily IPv6
```

---

# 28. Nmap and IPv6

Nmap supports IPv6 scanning.

Use:

```bash
nmap -6 <IPv6-address>
```

For an authorized lab target, for example:

```bash
nmap -6 -sV 2001:db8::10
```

`-6` tells Nmap to use IPv6.

> Only scan systems you own or have explicit permission to test.

---

# 29. IPv6 VAPT Checklist

During an authorized assessment:

### Discovery

```text
☐ IPv6 enabled?
☐ Global addresses?
☐ Link-local addresses?
☐ ULA addresses?
☐ IPv6 DNS records?
```

### Services

```text
☐ Services listening over IPv6?
☐ Same services exposed over IPv4 and IPv6?
☐ Unexpected IPv6 services?
```

### Firewall

```text
☐ IPv6 firewall enabled?
☐ Rules equivalent to IPv4?
☐ Inbound IPv6 filtered?
☐ Outbound IPv6 controlled?
```

### Network

```text
☐ Router Advertisements controlled?
☐ NDP protections?
☐ Segmentation?
☐ IPv6 routing controlled?
```

### Monitoring

```text
☐ IPv6 logs collected?
☐ IDS/IPS supports IPv6?
☐ SIEM receives IPv6 events?
```

---

# 30. Common IPv6 Security Issues

| Issue                      | Security Impact                    |
| -------------------------- | ---------------------------------- |
| IPv6 firewall missing      | Unexpected exposure                |
| Rogue RA                   | Traffic/configuration manipulation |
| Weak NDP protection        | Local network attacks              |
| Unmonitored IPv6           | Detection blind spots              |
| IPv4-only security testing | Missed attack surface              |
| Misconfigured dual stack   | Alternate attack path              |
| Exposed services           | Increased attack surface           |
| Poor IPv6 segmentation     | Easier lateral movement            |

---

# 31. IPv6 DNS Records

IPv6 uses DNS records called:

```text
AAAA
```

IPv4 uses:

```text
A
```

Example:

```text
example.com → A → IPv4
example.com → AAAA → IPv6
```

During authorized recon, checking both can reveal different network paths.

---

# 32. IPv4 vs IPv6 Recon Mental Model

Don't think:

```text
Find IPv4 → Done
```

Think:

```text
                Target
                  |
          ┌───────┴───────┐
          ↓               ↓
        IPv4             IPv6
          |               |
       DNS A           DNS AAAA
          |               |
       Services        Services
          |               |
      Firewall         Firewall
          |               |
       Routing          Routing
```

This is a much better VAPT mindset.

---

# 33. Practical Lab — Build an IPv6 Lab

Use your own Linux VM or another machine you control.

### Step 1 — Check IPv6

```bash
ip -6 addr
```

### Step 2 — Check routing

```bash
ip -6 route
```

### Step 3 — Test loopback

```bash
ping -6 ::1
```

### Step 4 — Check listening services

```bash
ss -lnt
```

### Step 5 — Identify IPv6 listeners

Look for addresses such as:

```text
[::]:80
[::]:443
```

### Goal

Understand:

```text
Address
   ↓
Interface
   ↓
Route
   ↓
Listening service
```

---

# 34. Practical Python Exercise

You can inspect IPv6 addresses using Python's standard library.

```python
import socket

hostname = socket.gethostname()

addresses = socket.getaddrinfo(
    hostname,
    None,
    socket.AF_INET6
)

for address in addresses:
    print(address[4][0])
```

This teaches an important concept:

```text
Python
 ↓
DNS / local resolver
 ↓
IPv6 address information
```

---

# 35. Beginner Mistakes

### ❌ Mistake 1

Thinking every IPv6 address is globally reachable.

### ✅ Correct

IPv6 has multiple address types, including:

```text
Global Unicast
Link-Local
ULA
Loopback
Multicast
```

---

### ❌ Mistake 2

Thinking IPv6 uses ARP.

### ✅ Correct

IPv6 uses:

```text
NDP over ICMPv6
```

---

### ❌ Mistake 3

Thinking `::` means `0.0.0.0`.

### ✅ Correct

`::` is the IPv6 **unspecified address**, while `0.0.0.0` is IPv4's unspecified address.

---

### ❌ Mistake 4

Thinking IPv6 is automatically encrypted.

### ✅ Correct

IPv6 can use IPsec, but encryption isn't automatic for all traffic.

---

### ❌ Mistake 5

Securing IPv4 but forgetting IPv6.

This is one of the most important VAPT mistakes.

---

# 36. Interview Questions

### Q1. How many bits does IPv6 use?

**Answer:** 128 bits.

### Q2. What is the IPv6 loopback address?

**Answer:**

```text
::1
```

### Q3. What is the IPv6 link-local range?

**Answer:**

```text
fe80::/10
```

### Q4. Does IPv6 use ARP?

**Answer:** No. IPv6 uses Neighbor Discovery Protocol (NDP) through ICMPv6.

### Q5. Does IPv6 have broadcast?

**Answer:** No. IPv6 uses multicast instead.

### Q6. What record represents IPv6 in DNS?

**Answer:** AAAA.

### Q7. What does `/64` mean?

**Answer:** The first 64 bits represent the prefix/network portion.

### Q8. What is SLAAC?

**Answer:** Stateless Address Autoconfiguration.

### Q9. What is IPv6 dual stack?

**Answer:** Running IPv4 and IPv6 simultaneously.

### Q10. Why is IPv6 important in VAPT?

**Answer:** Because IPv6 can provide additional addresses, routes, and services that may be missed by an IPv4-only assessment.

---

# 37. Scenario-Based Questions

### Scenario 1

A company has strong IPv4 firewall rules but no IPv6 firewall policy.

**What should a pentester investigate?**

Look for:

```text
IPv6 exposure
↓
IPv6 services
↓
IPv6 routes
↓
Firewall differences
↓
Monitoring differences
```

---

### Scenario 2

DNS contains:

```text
A     → IPv4 address
AAAA  → IPv6 address
```

What does this tell you?

The hostname may be reachable through both IPv4 and IPv6.

---

### Scenario 3

You see:

```text
[::]:443
```

What does this indicate?

A service is listening on an IPv6 wildcard address, subject to the operating system/application's socket behavior and configuration.

---

# 38. MCQs

### 1. IPv6 address size is:

A. 32-bit
B. 64-bit
C. 128-bit
D. 256-bit

**Answer: C**

---

### 2. IPv6 loopback is:

A. `127.0.0.1`
B. `::1`
C. `fe80::1`
D. `0.0.0.0`

**Answer: B**

---

### 3. IPv6 uses which protocol instead of ARP?

A. FTP
B. NDP
C. DHCP
D. SSH

**Answer: B**

---

### 4. IPv6 link-local addresses begin with:

A. `10::`
B. `fc00::`
C. `fe80::`
D. `ff00::`

**Answer: C**

---

### 5. IPv6 multicast addresses use:

A. `ff00::/8`
B. `fe80::/10`
C. `127::/8`
D. `10::/8`

**Answer: A**

---

### 6. DNS record for IPv6 is:

A. A
B. MX
C. NS
D. AAAA

**Answer: D**

---

### 7. IPv6 has:

A. Broadcast only
B. Broadcast and multicast
C. No broadcast; multicast is used
D. Neither

**Answer: C**

---

### 8. What does SLAAC provide?

A. Automatic IPv6 address configuration
B. Encryption
C. Port scanning
D. DNS filtering

**Answer: A**

---

### 9. Which command displays IPv6 addresses on Linux?

A. `ip -6 addr`
B. `ip -4 addr`
C. `route -v`
D. `arp -6`

**Answer: A**

---

### 10. Which Nmap option enables IPv6 scanning?

A. `-4`
B. `-6`
C. `-ip6`
D. `-ipv6`

**Answer: B**

---

# 39. Must-Memorize Cheat Sheet

```text
IPv6
↓
128-bit address

Loopback
↓
::1

Unspecified
↓
::

Link-local
↓
fe80::/10

ULA
↓
fc00::/7
Common local assignments
↓
fd00::/8

Multicast
↓
ff00::/8

IPv6 DNS
↓
AAAA

IPv4 address resolution
↓
ARP

IPv6 neighbor discovery
↓
NDP / ICMPv6

IPv6 configuration
↓
SLAAC / DHCPv6 / Manual

IPv6 scanning with Nmap
↓
nmap -6

Linux IPv6 addresses
↓
ip -6 addr

Linux IPv6 routes
↓
ip -6 route

IPv6 loopback test
↓
ping -6 ::1
```

---

# 40. Final Mental Model

Don't memorize IPv6 as just a long address.

Understand it as a complete networking system:

```text
                    IPv6
                     |
        ┌────────────┼────────────┐
        ↓            ↓            ↓
    Addressing     Routing      Security
        |            |            |
   Global/ULA    IPv6 routes    Firewall
   Link-local    Gateway        IDS/IPS
   Multicast     Prefixes       Monitoring
        |            |            |
        └────────────┼────────────┘
                     ↓
                  Services
                     |
              HTTP / SSH / DNS
                     |
                     ↓
                  VAPT
                     |
        ┌────────────┼────────────┐
        ↓            ↓            ↓
     Discovery    Enumeration   Exposure
        ↓            ↓            ↓
      IPv6        Services      Misconfig
```

The **most important VAPT lesson** is:

> **Never assume that securing and testing IPv4 automatically secures and tests IPv6. Treat IPv4 and IPv6 as separate attack surfaces that must both be understood and assessed.**

---

# Key Takeaways

* IPv6 uses **128-bit addresses**.
* IPv6 addresses are written using **hexadecimal hextets**.
* `::` compresses consecutive zero groups.
* `::1` = IPv6 loopback.
* `fe80::/10` = link-local.
* `fc00::/7` = ULA space.
* `ff00::/8` = multicast.
* IPv6 has **no broadcast**.
* IPv6 uses **NDP/ICMPv6 instead of ARP**.
* `AAAA` DNS records represent IPv6 addresses.
* SLAAC can automatically configure IPv6 addresses.
* IPv6 can use IPsec, but IPv6 traffic isn't automatically encrypted.
* Dual-stack environments create an additional security surface.
* VAPT should assess **IPv4 and IPv6 separately**.

## Practical Skill Target

After completing this lesson, you should be able to look at:

```text
2001:db8:1234::10/64
```

and understand:

```text
2001:db8:1234::10 → IPv6 address
/64               → prefix length
IPv6              → 128-bit addressing
```

Then connect that knowledge to:

```text
DNS → IPv6 → Routing → Service → Firewall → Monitoring → VAPT
```

