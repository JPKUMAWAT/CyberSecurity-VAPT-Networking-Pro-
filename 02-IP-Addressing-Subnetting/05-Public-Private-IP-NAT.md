# 05 – Public, Private IP & NAT

> **Goal:** Understand how devices communicate inside a private network, how they access the Internet using public IPs, and how NAT/PAT works.
>
> **Difficulty:** 🟢 Beginner → 🟡 Intermediate → 🔴 VAPT/Interview
>
> **Cybersecurity relevance:** ⭐⭐⭐⭐⭐

---

# 1. What Is an IP Address?

An **IP address (Internet Protocol address)** identifies a device/interface on an IP network.

Example:

```text
192.168.1.10
```

Think of it like an address used for network communication.

An IP address helps determine:

* Where a device is located logically
* Which network it belongs to
* Where packets should be delivered

---

# 2. IPv4 Address

IPv4 uses **32 bits**.

Example:

```text
192.168.1.10
```

It contains four octets:

```text
192 . 168 . 1 . 10
 ↓     ↓    ↓    ↓
 8     8    8    8 bits
```

Therefore:

```text
8 + 8 + 8 + 8 = 32 bits
```

IPv4 has approximately:

```text
2^32 = 4,294,967,296
```

possible addresses.

---

# 3. Public IP vs Private IP

This is one of the most important networking concepts.

## Public IP

A **public IP address** is globally routable on the Internet.

Example:

```text
8.8.8.8
```

Public IPs are generally assigned by an ISP or cloud/provider.

They can be used for Internet-facing services such as:

```text
Web Server
VPN Server
Mail Server
Public API
Cloud Server
```

---

# 4. Private IP

Private IP addresses are designed for use inside private networks.

The three IPv4 private ranges are:

| Private Range                   | CIDR             | Typical Use                  |
| ------------------------------- | ---------------- | ---------------------------- |
| `10.0.0.0 – 10.255.255.255`     | `10.0.0.0/8`     | Large organizations          |
| `172.16.0.0 – 172.31.255.255`   | `172.16.0.0/12`  | Enterprise/internal networks |
| `192.168.0.0 – 192.168.255.255` | `192.168.0.0/16` | Home/small networks          |

Examples:

```text
10.10.10.5
172.16.20.10
192.168.1.50
```

These addresses are **not directly Internet-routable**.

---

# 5. Important Difference

```text
Private Network
       |
       | Private IP
       ↓
   Router/NAT
       |
       | Public IP
       ↓
    Internet
```

Example:

```text
Laptop
192.168.1.10
     |
     ↓
Router
192.168.1.1
Public IP: 203.0.113.10
     |
     ↓
Internet
```

The laptop does not normally appear on the Internet as:

```text
192.168.1.10
```

Instead, the router performs address translation.

---

# 6. Why Private IPs Exist

If every device required a unique public IPv4 address, IPv4 address space would be insufficient.

Private addressing allows organizations to reuse the same address ranges internally.

For example:

Company A:

```text
192.168.1.10
```

Company B:

```text
192.168.1.10
```

Company C:

```text
192.168.1.10
```

This is completely normal because these are separate private networks.

---

# 7. What Is NAT?

**NAT = Network Address Translation**

NAT translates IP addresses between networks.

Most commonly:

```text
Private IP → Public IP
```

Example:

```text
192.168.1.10
      ↓
     NAT
      ↓
203.0.113.10
```

NAT is commonly performed by:

* Home routers
* Enterprise firewalls
* Cloud gateways
* Security appliances

---

# 8. Why NAT Is Used

NAT provides several practical benefits:

### 1. IPv4 Conservation

Many private devices can share a smaller number of public IPv4 addresses.

### 2. Internal Addressing

Organizations can use private addressing internally.

### 3. Network Architecture

NAT can separate internal networks from external networks.

### 4. Connectivity

Private devices can access Internet services through a NAT gateway.

---

# 9. Basic NAT Example

Suppose your laptop has:

```text
Private IP:
192.168.1.10
```

Your router has:

```text
LAN IP:
192.168.1.1

Public IP:
203.0.113.10
```

You visit:

```text
example.com
```

Simplified flow:

```text
Laptop
192.168.1.10
      |
      | Request
      ↓
Router/NAT
      |
      | Translated request
      ↓
Internet
```

The external server sees the router's public address rather than the laptop's private address.

---

# 10. NAT Translation Table

A NAT device keeps track of connections.

Simplified example:

| Inside Local         | Inside Global        | Destination |
| -------------------- | -------------------- | ----------- |
| `192.168.1.10:50001` | `203.0.113.10:40001` | Web server  |
| `192.168.1.20:50002` | `203.0.113.10:40002` | DNS server  |

This allows multiple internal systems to communicate externally.

---

# 11. PAT – Port Address Translation

**PAT = Port Address Translation**

PAT is a very common form of NAT.

It allows multiple private devices to share **one public IPv4 address** by using different port numbers.

Example:

```text
192.168.1.10:51500
        ↓
203.0.113.10:40001

192.168.1.20:51501
        ↓
203.0.113.10:40002
```

Both devices use:

```text
203.0.113.10
```

but different translated ports.

PAT is sometimes called:

```text
NAT Overload
```

---

# 12. NAT vs PAT

| NAT                                         | PAT                                              |
| ------------------------------------------- | ------------------------------------------------ |
| Translates addresses                        | Translates address + ports                       |
| Can use one-to-one mappings                 | Many devices can share one public IP             |
| General concept                             | Common implementation for Internet access        |
| Port translation isn't necessarily required | Port numbers are used to distinguish connections |

---

# 13. Types of NAT

Common NAT terminology includes:

### Static NAT

One private IP maps to one public IP.

```text
192.168.1.10
      ↕
203.0.113.10
```

Useful when an internal service needs a predictable public mapping.

---

### Dynamic NAT

Private addresses are mapped to addresses from a public pool.

Example:

```text
Private IP
    ↓
Public IP Pool
    ↓
203.0.113.10
203.0.113.11
203.0.113.12
```

---

### PAT

Many private devices share one public IP using different ports.

```text
192.168.1.10:5000
          ↓
203.0.113.10:40001

192.168.1.20:5000
          ↓
203.0.113.10:40002
```

---

# 14. Source NAT – SNAT

**SNAT = Source Network Address Translation**

The source address of an outgoing packet is changed.

Example:

```text
Before:

Source:
192.168.1.10

Destination:
8.8.8.8
```

After NAT:

```text
Source:
203.0.113.10

Destination:
8.8.8.8
```

This is commonly used for internal clients accessing the Internet.

---

# 15. Destination NAT – DNAT

**DNAT = Destination Network Address Translation**

The destination address is translated.

Example:

```text
Internet
   |
   ↓
203.0.113.10:443
   |
   | DNAT
   ↓
192.168.1.100:443
```

This is commonly used for publishing an internal service through a public address.

---

# 16. Port Forwarding

Port forwarding is commonly implemented using DNAT.

Example:

```text
Public:

203.0.113.10:443
        |
        ↓
Firewall/Router
        |
        ↓
192.168.1.100:443
```

The router forwards incoming traffic to the internal server.

---

# 17. NAT Does NOT Equal Firewall

This is a very important cybersecurity concept.

NAT:

```text
Translates addresses/ports
```

Firewall:

```text
Allows or blocks traffic according to rules
```

They can work together, but they are not the same thing.

Example:

```text
Internet
   ↓
Firewall
   ↓
NAT
   ↓
Internal Network
```

---

# 18. Does NAT Make a Network Secure?

**No.**

NAT can reduce direct exposure of private hosts, but NAT itself is **not a complete security mechanism**.

Security should also use:

* Firewall rules
* Access control
* Network segmentation
* Authentication
* Monitoring
* IDS/IPS
* Secure configuration
* Patch management

---

# 19. VAPT Perspective

Understanding public/private IPs is extremely important during network penetration testing.

Suppose a client gives you:

```text
203.0.113.20
```

This may be their public-facing asset.

But internally they may have:

```text
10.10.0.0/16
```

The pentester needs to understand the boundary between:

```text
Internet
   ↓
Public Network
   ↓
Firewall/NAT
   ↓
DMZ
   ↓
Internal Network
```

---

# 20. Public Attack Surface

A public IP may expose services such as:

```text
80    HTTP
443   HTTPS
22    SSH
25    SMTP
53    DNS
```

During an authorized assessment, the tester may identify:

* Open ports
* Exposed services
* Service versions
* TLS configuration
* Authentication interfaces
* Misconfigured public services

Always test only systems that are explicitly authorized.

---

# 21. Private Network Assessment

After obtaining authorized internal access, a tester may encounter:

```text
10.0.0.0/8

172.16.0.0/12

192.168.0.0/16
```

The tester should understand:

```text
Subnet
   ↓
Hosts
   ↓
Services
   ↓
Network Segmentation
   ↓
Access Controls
```

---

# 22. DMZ

**DMZ = Demilitarized Zone**

A DMZ is a network segment commonly used for systems that need controlled external exposure.

Example:

```text
                 INTERNET
                    |
                    ↓
              [ Firewall ]
               /        \
              /          \
             ↓            ↓
           DMZ          Internal
            |              |
        Web Server       Database
        Mail Server      AD Server
```

A web server might be placed in the DMZ while the database remains in an internal network.

---

# 23. Why DMZ Matters in VAPT

A tester may assess whether:

```text
Internet → DMZ
```

is allowed as intended.

But more importantly:

```text
DMZ → Internal
```

should be tightly controlled.

A serious segmentation weakness could allow an attacker who compromises a public-facing system to reach internal resources.

---

# 24. NAT and Network Visibility

NAT can change what external observers see.

Example:

Internal:

```text
192.168.1.10
192.168.1.11
192.168.1.12
```

External:

```text
203.0.113.10
```

From the Internet, those systems may appear behind the same public IP.

Therefore:

```text
Public IP ≠ Number of Internal Hosts
```

One public IP can represent many internal devices.

---

# 25. Important Special IPv4 Ranges

Not every non-public-looking IP is a normal private address.

### Private

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

### Loopback

```text
127.0.0.0/8
```

Common example:

```text
127.0.0.1
```

Used by a host to communicate with itself.

---

### Link-Local / APIPA

```text
169.254.0.0/16
```

Often automatically assigned when a device cannot obtain an IPv4 address through DHCP.

---

### Unspecified

```text
0.0.0.0
```

Meaning depends on context.

Examples:

```text
0.0.0.0/0
```

represents the IPv4 default route.

A server binding to:

```text
0.0.0.0
```

usually means listening on all available IPv4 interfaces.

---

# 26. RFC 1918 Private Address Ranges

The official IPv4 private ranges are:

```text
10.0.0.0/8

172.16.0.0/12

192.168.0.0/16
```

Important:

Not every `172.x.x.x` address is private.

Only:

```text
172.16.0.0
through
172.31.255.255
```

is RFC1918 private space.

For example:

```text
172.20.10.5 → Private

172.40.10.5 → Not RFC1918 private
```

---

# 27. Common Beginner Mistake

❌ Wrong:

```text
172.0.0.0/8 = Private
```

✅ Correct:

```text
172.16.0.0/12 = Private
```

---

# 28. Another Important Point

A private IP does not automatically mean:

```text
"Safe"
```

Example:

```text
192.168.1.50
```

could host:

```text
SSH
HTTP
SMB
Database
Admin panel
```

It is still an important security asset.

---

# 29. NAT and Inbound Connections

Normally:

```text
Internal Client
      ↓
Internet
```

is easy to support with NAT/PAT.

But:

```text
Internet
    ↓
Internal Client
```

is different.

The router/firewall needs an appropriate rule, such as port forwarding/DNAT, for unsolicited inbound traffic to reach an internal service.

---

# 30. NAT Reflection / Hairpin NAT

Sometimes an internal client accesses an organization's public address to reach an internal service.

Example:

```text
Internal Client
192.168.1.20
      |
      ↓
Public IP
203.0.113.10
      |
      ↓
Internal Web Server
192.168.1.100
```

This behavior is commonly called:

```text
NAT reflection
```

or:

```text
Hairpin NAT
```

depending on the implementation/context.

---

# 31. NAT in Cloud Environments

Cloud networks also commonly use private addressing and NAT gateways.

Example:

```text
Private Cloud Instance
10.0.2.15
       |
       ↓
NAT Gateway
       |
       ↓
Internet
```

The private instance can access external services without necessarily having a public IP assigned directly to it.

---

# 32. VAPT Workflow Example

Suppose the authorized scope contains:

```text
203.0.113.50
```

The tester identifies:

```text
443/tcp open
80/tcp open
```

The tester should understand:

```text
Public IP
    ↓
Firewall
    ↓
NAT / Load Balancer
    ↓
Web Server
    ↓
Internal Services
```

The visible IP may not directly identify the physical/internal server.

There may be:

* NAT
* Reverse proxy
* Load balancer
* CDN
* WAF
* Firewall

between the tester and backend.

---

# 33. NAT vs Reverse Proxy

These are not the same.

### NAT

Primarily translates network addresses/ports.

### Reverse Proxy

Receives application-layer requests and forwards them to backend servers.

Example:

```text
Client
  ↓
Reverse Proxy
  ↓
Web Server
  ↓
Application
```

A reverse proxy can provide:

* TLS termination
* Load balancing
* Authentication
* Routing
* Security filtering

---

# 34. NAT vs VPN

NAT:

```text
Changes address/port information
```

VPN:

```text
Creates a protected communication tunnel
```

They solve different problems.

A VPN may itself use NAT in some architectures.

---

# 35. Practical Linux Commands

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

### Show default gateway

```bash
ip route | grep default
```

### Test connectivity

```bash
ping 192.168.1.1
```

### Check public IP

You can use a trusted external service when appropriate, but remember:

```text
Your local interface IP
≠
Your public Internet IP
```

---

# 36. Practical Windows Commands

### Show IP configuration

```cmd
ipconfig
```

### Detailed information

```cmd
ipconfig /all
```

### View routes

```cmd
route print
```

### Test gateway

```cmd
ping 192.168.1.1
```

---

# 37. How to Identify Your Network

Suppose:

```text
IP:
192.168.1.50

Mask:
255.255.255.0
```

This corresponds to:

```text
192.168.1.50/24
```

Network:

```text
192.168.1.0/24
```

Usable range:

```text
192.168.1.1
-
192.168.1.254
```

Broadcast:

```text
192.168.1.255
```

---

# 38. NAT Packet Flow

Example:

```text
Laptop
192.168.1.10:51500
       |
       ↓
Router
       |
       | NAT/PAT
       ↓
203.0.113.10:40001
       |
       ↓
Internet Server
8.8.8.8:443
```

Return traffic:

```text
8.8.8.8:443
       ↓
203.0.113.10:40001
       ↓
NAT table
       ↓
192.168.1.10:51500
```

The NAT device uses its connection state/table to associate the return traffic with the internal client.

---

# 39. Security Implications of NAT

NAT can affect:

### Asset Discovery

Multiple internal hosts may share one public address.

### Attribution

Logs may show the NAT public IP.

Therefore organizations often need:

* NAT translation logs
* Firewall logs
* DHCP logs
* Authentication logs

to identify the actual internal device/user.

---

# 40. NAT and Logging

Suppose a web server sees:

```text
203.0.113.10
```

It may not know which internal machine generated the request.

The firewall/NAT device may have a translation record:

```text
203.0.113.10:40001
        ↓
192.168.1.10:51500
```

This is why timestamps and source ports can be important during incident investigation.

---

# 41. Security Misconfiguration Example

Imagine:

```text
Internet
   ↓
Public IP
   ↓
Port Forwarding
   ↓
Internal Admin Panel
```

If an administrative interface is unnecessarily exposed to the Internet, it increases attack surface.

During an authorized VAPT, testers may evaluate:

* Is exposure required?
* Is authentication strong?
* Is MFA enabled?
* Is access restricted?
* Is TLS configured correctly?
* Are unnecessary services exposed?

---

# 42. Important VAPT Mental Model

Don't think:

```text
Public IP = Server
```

Think:

```text
Public IP
   ↓
Firewall / NAT
   ↓
Load Balancer / Reverse Proxy
   ↓
Web Server
   ↓
Application
   ↓
Database
```

The actual architecture can be much more complex.

---

# 43. Quick Comparison

| Feature           | Public IP               | Private IP       |
| ----------------- | ----------------------- | ---------------- |
| Internet-routable | Yes                     | No               |
| Used internally   | Yes                     | Yes              |
| Globally unique   | Yes                     | No               |
| RFC1918           | No                      | Yes              |
| Example           | `8.8.8.8`               | `192.168.1.10`   |
| Common use        | Internet-facing systems | Internal devices |

---

# 44. Quick NAT Comparison

| Concept         | Meaning                             |
| --------------- | ----------------------------------- |
| NAT             | Address translation                 |
| SNAT            | Changes source address              |
| DNAT            | Changes destination address         |
| PAT             | Uses ports to allow address sharing |
| Port Forwarding | Usually DNAT-based                  |
| Static NAT      | Fixed one-to-one mapping            |
| Dynamic NAT     | Uses a public address pool          |

---

# 45. Interview Questions

## Q1. What is a private IP?

An IP address reserved for private network use and not directly routable on the public Internet.

---

## Q2. What are the RFC1918 private IPv4 ranges?

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

---

## Q3. What is NAT?

NAT translates IP addresses between networks.

---

## Q4. What is PAT?

PAT allows multiple private hosts to share a public IP by using different port mappings.

---

## Q5. Difference between SNAT and DNAT?

```text
SNAT → Source address changes

DNAT → Destination address changes
```

---

## Q6. Does NAT provide complete security?

No.

NAT is primarily an address/port translation mechanism. Security requires additional controls such as firewalls, segmentation, authentication and monitoring.

---

## Q7. Why are private IP addresses used?

Primarily to conserve IPv4 address space and provide internal network addressing.

---

## Q8. Can two different organizations use the same private IP?

Yes.

For example:

```text
Company A → 192.168.1.10

Company B → 192.168.1.10
```

because private addresses are not globally unique.

---

## Q9. What is a DMZ?

A controlled network segment commonly used for systems that require external exposure while remaining separated from the internal network.

---

## Q10. Why is NAT important in VAPT?

Because it affects:

* Asset visibility
* Network boundaries
* Public exposure
* Port forwarding
* Source-IP interpretation
* Internal/external attack surfaces

---

# 46. Common Quiz Questions

### Question 1

Which is a private IP?

A. `8.8.8.8`
B. `172.20.10.5`
C. `1.1.1.1`
D. `172.40.10.5`

**Answer: B**

---

### Question 2

Which range is private?

A. `172.0.0.0/8`
B. `172.16.0.0/12`
C. `173.16.0.0/16`
D. `169.0.0.0/8`

**Answer: B**

---

### Question 3

What does PAT use to distinguish multiple connections?

A. MAC address only
B. Ports
C. DNS names
D. Subnet masks

**Answer: B**

---

### Question 4

What does SNAT modify?

A. Destination
B. Source
C. DNS record
D. MAC address

**Answer: B**

---

### Question 5

What does DNAT modify?

A. Source
B. Destination
C. Username
D. Password

**Answer: B**

---

### Question 6

Is `192.168.1.10` publicly routable?

A. Yes
B. No

**Answer: B**

---

### Question 7

What is `127.0.0.1`?

A. Public IP
B. Private LAN IP
C. Loopback address
D. Broadcast address

**Answer: C**

---

### Question 8

Which device commonly performs NAT?

A. Router
B. Firewall
C. NAT gateway
D. All of these can

**Answer: D**

---

### Question 9

Does NAT automatically mean a host is secure?

A. Yes
B. No

**Answer: B**

---

### Question 10

What is commonly used to expose an internal service through a public port?

A. Port forwarding
B. DHCP
C. ARP
D. ICMP

**Answer: A**

---

# 47. Must-Memorize Cheat Sheet

```text
PRIVATE IPv4
────────────────────────────

10.0.0.0/8

172.16.0.0/12

192.168.0.0/16
```

```text
SPECIAL
────────────────────────────

127.0.0.0/8     → Loopback

169.254.0.0/16  → Link-local

0.0.0.0         → Unspecified / context-dependent
```

```text
NAT
────────────────────────────

NAT  → Address Translation

SNAT → Source Translation

DNAT → Destination Translation

PAT  → Address + Port Translation
```

---

# 48. Final Mental Model

Remember this:

```text
             INTERNET
                 |
          Public IP Address
                 |
          Firewall / Router
                 |
            NAT / PAT
                 |
       ┌─────────┴─────────┐
       ↓                   ↓
 Private Network       DMZ Network
       |                   |
       ↓                   ↓
 Laptops/Servers      Public Services
       |
       ↓
 Internal Systems
```

The key idea is:

> **Public IPs provide Internet-facing addressing. Private IPs provide internal addressing. NAT/PAT translates traffic between these addressing domains.**

For VAPT, understanding this architecture helps you identify **where the real attack surface is, where network boundaries exist, and how public-facing systems connect to internal infrastructure.**
