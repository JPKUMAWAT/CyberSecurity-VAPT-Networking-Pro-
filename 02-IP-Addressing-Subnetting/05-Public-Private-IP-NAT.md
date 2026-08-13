

## 1. What Is an IP Address?

An **IP (Internet Protocol) address** identifies a network interface so devices can communicate using IP.

Think of it like an address:

```text
Device
   │
   └── IP Address
          │
          └── tells the network where traffic should go
```

For IPv4, an address contains **32 bits**.

Example:

```text
192.168.1.10
```

It is divided into four octets:

```text
192    .    168    .    1    .    10
8 bits      8 bits      8 bits      8 bits
```

Total:

```text
8 + 8 + 8 + 8 = 32 bits
```

---

# 2. Public vs Private IP

The most important distinction:

| Feature                     | Private IP             | Public IP       |
| --------------------------- | ---------------------- | --------------- |
| Used                        | Internal network       | Internet        |
| Globally unique             | ❌ No                   | ✅ Generally yes |
| Routable on public Internet | ❌ No                   | ✅ Yes           |
| Example                     | `192.168.1.10`         | `203.0.113.10`* |
| Common use                  | Laptop, phone, printer | Router/server   |
| NAT commonly involved       | ✅                      | Often           |

* `203.0.113.0/24` is a documentation/example range, not a normal public address for your real Internet connection.

---

# 3. Private IPv4 Address Ranges

There are **three RFC 1918 private IPv4 ranges**:

| Range                           | CIDR  | Common usage        |
| ------------------------------- | ----- | ------------------- |
| `10.0.0.0 – 10.255.255.255`     | `/8`  | Large networks      |
| `172.16.0.0 – 172.31.255.255`   | `/12` | Medium networks     |
| `192.168.0.0 – 192.168.255.255` | `/16` | Home/small networks |

### Easy memory

```text
10.x.x.x
172.16.x.x → 172.31.x.x
192.168.x.x
```

Example:

```text
Laptop     → 192.168.1.10
Phone      → 192.168.1.11
Printer    → 192.168.1.20
Router     → 192.168.1.1
```

These devices can communicate internally without having individually routable public IPv4 addresses.

---

# 4. Important: Private ≠ Automatically Secure

This is a common beginner mistake.

Having:

```text
192.168.1.10
```

does **not** mean the device is automatically secure.

Security depends on things such as:

* Firewall rules
* Authentication
* Network segmentation
* Services exposed
* Router configuration
* Vulnerabilities
* Access-control policies

### VAPT perspective

An internal private IP can still expose:

```text
22  → SSH
80  → HTTP
445 → SMB
3306 → MySQL
```

An attacker who gains internal network access may be able to enumerate these services.

---

# 5. What Is a Public IP?

A public IP is an address used for communication across the public Internet.

Example architecture:

```text
                    INTERNET
                       │
                Public IP
                       │
                 ┌─────┴─────┐
                 │   Router  │
                 └─────┬─────┘
                       │
              Private Network
                       │
          ┌────────────┼────────────┐
          │            │            │
      192.168.1.10  192.168.1.11  192.168.1.12
        Laptop         Phone        PC
```

The router commonly performs NAT between the private network and Internet.

---

# 6. Why Do We Need NAT?

IPv4 has a limited address space.

Instead of assigning every internal device its own public IPv4 address, organizations can use private addresses internally.

For example:

```text
100 devices
      │
      ▼
Private IPs
      │
      ▼
    Router
      │
      ▼
One/few public IPs
      │
      ▼
  Internet
```

This helps conserve public IPv4 addresses.

---

# 7. What Is NAT?

**NAT = Network Address Translation**

NAT modifies IP addressing information as traffic passes through a NAT device.

Typical example:

```text
Laptop
192.168.1.10:51520
        │
        ▼
      Router
        │
        │ NAT
        ▼
Public-IP:40001
        │
        ▼
     Internet
```

The router maintains translation state so return traffic can be associated with the correct internal connection.

---

# 8. Basic NAT Flow

Suppose your laptop wants to access a web server.

```text
Internal network                 Internet

192.168.1.10
      │
      │ HTTP/HTTPS request
      ▼
192.168.1.1
 Router/NAT
      │
      │ translated traffic
      ▼
Public IP
      │
      ▼
Web Server
```

The web server generally does not directly see:

```text
192.168.1.10
```

because that is a private address.

It normally sees the public source address used by the NAT gateway.

---

# 9. NAT vs Routing

These concepts are related but **not the same**.

### Routing

Routing decides:

> "Where should this packet go?"

### NAT

NAT changes address information:

> "Which address should appear on this side of the network boundary?"

Example:

```text
Routing:
192.168.1.10 → Router → Internet

NAT:
192.168.1.10 → Public-IP
```

A router can route traffic without performing NAT.

---

# 10. PAT — Port Address Translation

You will often hear:

**NAT overload / PAT**

PAT allows many internal devices to share one public IPv4 address by using different source ports.

Example:

```text
Laptop:
192.168.1.10:50001
        │
        ▼
     Router
        │
        ▼
203.0.113.50:40001
```

Another device:

```text
Phone:
192.168.1.11:50002
        │
        ▼
     Router
        │
        ▼
203.0.113.50:40002
```

Same public IP, different translated ports.

---

# 11. Why PAT Is So Important

Imagine:

```text
50 internal devices
        │
        ▼
   One public IPv4
```

PAT can distinguish their connections using port numbers.

Conceptually:

```text
192.168.1.10:50001
        ↓
203.0.113.50:40001

192.168.1.11:50002
        ↓
203.0.113.50:40002
```

The NAT device tracks these mappings.

---

# 12. NAT Types — Important Concept

You may encounter different classifications of NAT.

Common concepts include:

### Static NAT

One private address maps to one public address.

```text
192.168.1.10
      ↕
203.0.113.10
```

Useful when an internal service needs a consistent public mapping.

---

### Dynamic NAT

Private addresses are translated using a pool of public addresses.

```text
Private IP
    ↓
NAT Pool
    ↓
Available Public IP
```

---

### PAT

Many private devices share one public IP using port translation.

```text
192.168.1.10:50001 ─┐
                    ├──► 203.0.113.50
192.168.1.11:50002 ─┤
                    │
192.168.1.12:50003 ─┘
```

PAT is extremely common in home and enterprise IPv4 networks.

---

# 13. NAT Does NOT Equal Firewall

Very important interview point:

> **NAT and firewalling are different functions.**

A firewall controls traffic according to security policy.

NAT translates addresses/ports.

A typical home router may perform both:

```text
Router
 ├── NAT/PAT
 ├── Firewall
 ├── Routing
 └── DHCP
```

But conceptually they are separate functions.

---

# 14. Does NAT Provide Security?

NAT can make unsolicited inbound connectivity more difficult in common configurations, especially when combined with stateful firewall behavior.

But:

> **NAT is not a security control by itself.**

Do not write:

❌ "NAT protects the network from hackers."

Better:

✅ "NAT translates addresses and, in common deployments, works alongside firewall/stateful filtering that controls inbound connections."

---

# 15. Port Forwarding

Suppose you have:

```text
Internet
   │
   ▼
Public IP
   │
   ▼
Router
   │
   ▼
192.168.1.50:80
```

A port-forwarding rule can tell the router to forward incoming traffic to an internal service.

Example concept:

```text
Public-IP:8080
      │
      ▼
Router
      │
      ▼
192.168.1.50:80
```

### VAPT relevance

Exposed port-forwarding rules can increase attack surface.

A pentester should determine:

* Which services are Internet-facing?
* Which ports are exposed?
* Is authentication enabled?
* Is the service patched?
* Is exposure actually required?
* Is access restricted by firewall/VPN/IP allowlists?

Only perform this testing with authorization.

---

# 16. Hairpin NAT / NAT Loopback

Interesting intermediate concept.

Suppose an internal user accesses a service using its public hostname:

```text
Internal Client
      │
      ▼
Public IP
      │
      ▼
Router
      │
      ▼
Internal Server
```

The router may support **NAT loopback/hairpin NAT**, allowing the internal client to reach an internal service through the public address.

This is useful to understand when troubleshooting:

* Internal vs external access
* DNS behavior
* Port forwarding
* Web applications

---

# 17. VAPT Perspective — Why Public/Private IP Matters

During an authorized assessment, determining network boundaries is important.

Example:

```text
                    INTERNET
                       │
                 Public IP
                       │
                ┌──────┴──────┐
                │ Perimeter FW│
                └──────┬──────┘
                       │
                 DMZ / Servers
                       │
                ┌──────┴──────┐
                │ Internal LAN │
                └──────┬──────┘
                       │
                User Workstations
```

A tester wants to understand:

### External attack surface

What is reachable from the Internet?

### Internal attack surface

What becomes reachable after obtaining internal network access?

### Segmentation

Can a system in one network reach systems in another?

---

# 18. Public vs Private Enumeration

### External assessment

You might identify:

```text
Public IP
   ↓
Open ports
   ↓
Services
   ↓
Versions
   ↓
Potential vulnerabilities
```

### Internal assessment

You may see:

```text
Private subnet
      ↓
Hosts
      ↓
Open services
      ↓
Network shares
      ↓
Segmentation controls
```

This is why IP addressing knowledge is fundamental to VAPT.

---

# 19. Find Your IP — Windows

Open CMD:

```cmd
ipconfig
```

Example:

```text
IPv4 Address . . . . . : 192.168.1.10
Default Gateway . . . : 192.168.1.1
```

PowerShell:

```powershell
Get-NetIPConfiguration
```

---

# 20. Find Your IP — Kali/Linux

```bash
ip addr
```

or:

```bash
ip a
```

Find routing information:

```bash
ip route
```

Example:

```text
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0
```

Interpretation:

```text
Your network:
192.168.1.0/24

Gateway:
192.168.1.1
```

---

# 21. Find Your Public IP

Your public IP is assigned/advertised by your Internet connection/provider and can differ from your device's private IP.

For a lab or your own machine, you can use a reputable "what is my IP" service or check your router's WAN information.

Remember:

```text
Private IP ≠ Public IP
```

---

# 22. Default Gateway

The **default gateway** is the router used to reach destinations outside the local subnet.

Example:

```text
Laptop
192.168.1.10
      │
      ▼
Gateway
192.168.1.1
      │
      ▼
Internet
```

If the destination isn't on the local subnet, the host normally sends the packet toward its default gateway.

---

# 23. Local vs Remote Communication

Suppose:

```text
PC A = 192.168.1.10/24
PC B = 192.168.1.20/24
```

They are in the same subnet.

Traffic can normally stay within the local network.

But:

```text
PC A = 192.168.1.10/24
Server = 8.8.8.8
```

The destination is outside the local subnet.

Traffic goes toward the default gateway.

```text
PC
 ↓
Gateway
 ↓
Router(s)
 ↓
Destination
```

---

# 24. ARP + Private Networks

For IPv4 local-network communication, **ARP** can map an IPv4 address to a MAC address.

Example:

```text
192.168.1.20
      ↓
    ARP
      ↓
AA:BB:CC:DD:EE:FF
```

This is important because IP addressing and Ethernet addressing work together on a typical LAN.

### VAPT relevance

ARP-related concepts become important when learning:

* Local network enumeration
* Network segmentation
* Traffic analysis
* ARP spoofing concepts

Do not perform spoofing against networks without explicit authorization.

---

# 25. CGNAT — Important Advanced Concept

Sometimes your router does **not** receive a globally routable public IPv4 address.

Your ISP may use:

**CGNAT = Carrier-Grade NAT**

One special range commonly associated with CGNAT is:

```text
100.64.0.0/10
```

This is the **Shared Address Space** defined for carrier networks.

Architecture:

```text
Your Device
192.168.1.10
      │
      ▼
Home Router
      │
      ▼
ISP CGNAT
100.64.x.x
      │
      ▼
Public Internet
```

### Why it matters

CGNAT can affect:

* Hosting services
* Port forwarding
* Incoming connections
* Remote access
* Network troubleshooting

---

# 26. Private IP Ranges ≠ All Special IPv4 Ranges

Don't memorize every special address as "private."

Examples:

```text
10.0.0.0/8        → Private
172.16.0.0/12     → Private
192.168.0.0/16    → Private
```

But:

```text
127.0.0.0/8       → Loopback
169.254.0.0/16    → Link-local
100.64.0.0/10     → Shared Address Space / CGNAT
```

These have different purposes.

---

# 27. Loopback

IPv4 loopback:

```text
127.0.0.0/8
```

Most commonly:

```text
127.0.0.1
```

Meaning:

> This machine itself.

Example:

```text
Browser
   │
   ▼
127.0.0.1:8080
   │
   ▼
Local application
```

Useful for:

* Local development
* Testing services
* Understanding network bindings

---

# 28. Link-Local Address

IPv4 link-local range:

```text
169.254.0.0/16
```

Example:

```text
169.254.20.5
```

A host may automatically assign a link-local address when normal DHCP configuration is unavailable.

It is generally intended for communication on the local link, not normal Internet routing.

---

# 29. VAPT Example: Network Boundary Analysis

Imagine an organization has:

```text
Internet
   │
   ▼
203.0.113.20
   │
   ▼
Firewall
   │
   ├── DMZ
   │    ├── Web Server
   │    └── Mail Server
   │
   └── Internal
        ├── 10.10.10.0/24
        └── 10.10.20.0/24
```

A professional assessment asks:

```text
What is externally exposed?
        ↓
What is internally reachable?
        ↓
Are networks segmented?
        ↓
Can traffic cross boundaries?
        ↓
Are unnecessary services exposed?
```

This is much more useful than simply memorizing IP ranges.

---

# 30. Common Beginner Mistakes

### ❌ Mistake 1

"192.168.x.x means secure."

**Correction:** Private addressing only describes address scope.

---

### ❌ Mistake 2

"NAT is a firewall."

**Correction:** NAT and firewalling are different functions.

---

### ❌ Mistake 3

"Every public IP belongs directly to a computer."

**Correction:** It may belong to a router, firewall, load balancer, cloud service, NAT gateway, etc.

---

### ❌ Mistake 4

"Private IPs can never communicate with the Internet."

**Correction:** They commonly access the Internet through a NAT gateway/router.

---

### ❌ Mistake 5

"Public IP always means the device is directly reachable."

**Correction:** Firewall/NAT/routing rules can prevent inbound access.

---

# 31. Interview Questions

### Q1. What is a private IP?

An IPv4 address reserved for use within private networks and not directly routable across the public Internet.

### Q2. Give the three RFC 1918 ranges.

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

### Q3. What is NAT?

Network Address Translation; it translates addressing information between network contexts, commonly between private and public IPv4 addressing.

### Q4. What is PAT?

Port Address Translation allows multiple internal connections/devices to share a public IPv4 address using port mappings.

### Q5. Is NAT a firewall?

No.

### Q6. What is a default gateway?

The router/interface used by a host to reach destinations outside its local subnet.

### Q7. What is CGNAT?

Carrier-Grade NAT used by ISPs to share public IPv4 addresses among customers.

### Q8. What is `127.0.0.1`?

IPv4 loopback address referring to the local host.

---

# 32. Scenario-Based Questions

### Scenario 1

Your laptop has:

```text
192.168.1.10
```

Your router has:

```text
192.168.1.1
```

Can your laptop access the Internet?

**Answer:** Yes, normally through the router/default gateway and NAT if required.

---

### Scenario 2

A web server has:

```text
10.0.0.50
```

Can you assume it is accessible directly from the public Internet?

**Answer:** No. `10.0.0.0/8` is private; Internet access would require appropriate routing/NAT/proxying, and inbound accessibility depends on network controls.

---

### Scenario 3

Two laptops have:

```text
192.168.1.10/24
192.168.1.20/24
```

Are they in the same IPv4 subnet?

**Answer:** Yes.

---

### Scenario 4

You see:

```text
169.254.10.20
```

What does it suggest?

**Answer:** An IPv4 link-local address, often associated with failure to obtain normal DHCP configuration.

---

# 33. MCQs

### 1. Which is a private IPv4 range?

A. `8.8.8.0/24`
B. `10.0.0.0/8`
C. `1.1.1.0/24`
D. `172.0.0.0/16`

**Answer: B**

---

### 2. What does NAT stand for?

A. Network Access Transfer
B. Network Address Translation
C. Network Authentication Technology
D. Network Allocation Table

**Answer: B**

---

### 3. Which address represents loopback?

A. `192.168.1.1`
B. `10.0.0.1`
C. `127.0.0.1`
D. `169.254.1.1`

**Answer: C**

---

### 4. Which technology allows many private hosts to share one public IPv4 address using ports?

A. DNS
B. PAT
C. ARP
D. DHCP

**Answer: B**

---

### 5. What is the default gateway used for?

A. Encrypting packets
B. Reaching destinations outside the local network
C. Assigning MAC addresses
D. Storing DNS records

**Answer: B**

---

### 6. Which range is used for IPv4 link-local addressing?

A. `127.0.0.0/8`
B. `169.254.0.0/16`
C. `192.168.0.0/16`
D. `100.64.0.0/10`

**Answer: B**

---

### 7. Is NAT itself a firewall?

A. Yes
B. No
C. Only with IPv6
D. Only with DNS

**Answer: B**

---

### 8. Which range is associated with CGNAT/shared address space?

A. `100.64.0.0/10`
B. `127.0.0.0/8`
C. `224.0.0.0/4`
D. `192.0.2.0/24`

**Answer: A**

---

# 34. Practical Lab — Beginner

> Perform these exercises only on your own machine/lab.

### Lab 1 — Identify Your Network

Windows:

```cmd
ipconfig
```

Linux/Kali:

```bash
ip a
ip route
```

Record:

```text
IPv4:
Subnet:
Default Gateway:
Interface:
```

---

### Lab 2 — Identify Your Local Subnet

Suppose:

```text
IP:      192.168.1.20
Mask:    255.255.255.0
Gateway: 192.168.1.1
```

Determine:

```text
Network:
Broadcast:
Usable range:
```

Use your subnetting knowledge from the previous lesson.

---

### Lab 3 — Observe the Route

Linux:

```bash
ip route
```

Windows:

```cmd
route print
```

Understand:

```text
Destination
Gateway
Interface
Metric
```

---

### Lab 4 — Build a Safe Local Architecture

Create:

```text
Windows Host
      │
      ▼
Kali VM
      │
      ▼
Private/Host-only Network
```

Then identify the IP addresses assigned to each machine.

The goal is to understand **network boundaries**, not to attack anything.

---

# 35. Advanced Concepts to Learn Next

Once this lesson is comfortable, move toward:

```text
IPv4
 │
 ├── Subnetting
 │
 ├── Routing
 │
 ├── ARP
 │
 ├── DHCP
 │
 ├── NAT/PAT
 │
 ├── VLANs
 │
 ├── Firewalls
 │
 ├── VPNs
 │
 ├── IPv6
 │
 └── Network Segmentation
```

For VAPT:

```text
Network
   ↓
IP Discovery
   ↓
Port Discovery
   ↓
Service Enumeration
   ↓
Network Segmentation
   ↓
Attack Surface
   ↓
Vulnerability Assessment
```

---

# 36. Must-Memorize Cheat Sheet

```text
PRIVATE IPv4
────────────────────────
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16


SPECIAL
────────────────────────
127.0.0.0/8       Loopback
169.254.0.0/16    Link-local
100.64.0.0/10     CGNAT/shared address space


NAT
────────────────────────
Private ↔ translated address


PAT
────────────────────────
Many connections → one public IP
                  using ports


GATEWAY
────────────────────────
Route toward external networks


VAPT
────────────────────────
Public exposure
      ↓
Network boundary
      ↓
Reachable hosts
      ↓
Open ports
      ↓
Services
      ↓
Segmentation
      ↓
Security assessment
```

---

# 37. Final Mental Model

Don't memorize this lesson as only:

> **"Private IP = 192.168.x.x."**

Understand the complete picture:

```text
                  INTERNET
                      │
                Public Network
                      │
                Public Address
                      │
                ┌─────▼─────┐
                │ Firewall/ │
                │   Router  │
                │ NAT/PAT   │
                └─────┬─────┘
                      │
               Private Network
                      │
        ┌─────────────┼─────────────┐
        │             │             │
    Laptop          Phone         Server
192.168.1.10    192.168.1.20   192.168.1.50
        │             │             │
        └─────────────┼─────────────┘
                      │
               Default Gateway
```

The key idea is:

**Private IPs identify hosts inside a private network. Public addressing provides Internet-routable reachability. NAT/PAT commonly translates private traffic at the network boundary, while routing decides where packets go and firewalls enforce traffic policy.**

### Key Takeaways

* Know the **three RFC 1918 private ranges**.
* Understand **public vs private addressing**.
* Understand **NAT vs PAT vs routing vs firewall**.
* Know the role of the **default gateway**.
* Remember `127.0.0.1` = loopback.
* Remember `169.254.0.0/16` = IPv4 link-local.
* Remember `100.64.0.0/10` = shared address space commonly used for CGNAT.
* In VAPT, always think about **network boundaries + exposure + segmentation**.
* A private IP does **not** automatically mean a system is secure.
