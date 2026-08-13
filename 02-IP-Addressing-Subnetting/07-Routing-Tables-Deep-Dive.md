

## 1. What Is Routing?

**Routing** is the process of deciding **where a network packet should be sent next** so that it can reach its destination.

Simple example:

```text
Your PC
  |
  | Packet → 8.8.8.8
  v
Default Gateway
  |
  v
ISP Router
  |
  v
Internet
  |
  v
8.8.8.8
```

Your computer usually does **not** know the complete path to `8.8.8.8`.

Instead, it decides:

> "I don't have a direct route to this destination, so I'll send the packet to my default gateway."

---

# 2. What Is a Routing Table?

A **routing table** is a set of rules maintained by a host or router that tells it where to forward packets.

A simplified routing table might look like:

```text
Destination       Gateway          Interface
192.168.1.0/24    Direct           eth0
10.0.0.0/8        192.168.1.1      eth0
0.0.0.0/0         192.168.1.1      eth0
```

Think of it as a **map**:

```text
Destination → Where should I send it?
```

---

# 3. Basic Routing Terms

| Term               | Meaning                                        |
| ------------------ | ---------------------------------------------- |
| Destination        | Network/address the packet wants to reach      |
| Prefix/Mask        | Defines how large the destination network is   |
| Gateway / Next Hop | Router to which the packet is forwarded        |
| Interface          | Network interface used to send packet          |
| Metric             | Preference/cost associated with a route        |
| Default Route      | Route used when no more specific route matches |
| Route              | A rule describing how to reach a destination   |

---

# 4. Destination Network

Suppose your machine has:

```text
IP:      192.168.1.20
Mask:    255.255.255.0
```

Equivalent CIDR:

```text
192.168.1.20/24
```

The directly connected network is:

```text
192.168.1.0/24
```

So your computer knows:

```text
192.168.1.0 ───── Directly reachable
```

Example:

```text
PC:     192.168.1.20
Server: 192.168.1.50
```

Because both belong to:

```text
192.168.1.0/24
```

the PC can communicate with the server directly at Layer 2, normally using ARP for IPv4 address resolution.

---

# 5. What Is a Default Gateway?

The **default gateway** is normally the router that a host uses when it doesn't have a more specific route for the destination.

Example:

```text
PC
192.168.1.20
     |
     | 192.168.1.1
     v
Router
     |
     v
Internet
```

Routing table:

```text
0.0.0.0/0 → 192.168.1.1
```

This means:

> For destinations not covered by a more specific route, send traffic to `192.168.1.1`.

---

# 6. Default Route

For IPv4:

```text
0.0.0.0/0
```

For IPv6:

```text
::/0
```

These represent the **default route**.

Example:

```text
Destination     Gateway
0.0.0.0/0       192.168.1.1
```

If your computer wants to reach:

```text
8.8.8.8
```

and there is no more specific route, it uses:

```text
0.0.0.0/0
```

and sends the packet toward:

```text
192.168.1.1
```

---

# 7. Directly Connected Routes

Suppose:

```text
IP:
192.168.10.25/24
```

The machine automatically knows:

```text
192.168.10.0/24
```

is directly connected.

Therefore:

```text
192.168.10.50
```

is on the local network.

But:

```text
192.168.20.50
```

is not.

The second destination normally requires a router.

---

# 8. How Does a Host Choose a Route?

This is one of the **most important concepts**.

Suppose the routing table contains:

```text
10.0.0.0/8
10.10.0.0/16
10.10.20.0/24
0.0.0.0/0
```

Destination:

```text
10.10.20.50
```

Several routes match.

Which one wins?

## Longest Prefix Match

The route with the **most specific matching prefix** is preferred.

```text
10.10.20.0/24     ← Most specific
10.10.0.0/16
10.0.0.0/8
0.0.0.0/0         ← Least specific
```

Therefore:

```text
10.10.20.0/24
```

wins.

### Must Remember

> **More specific route → preferred over less specific matching route.**

---

# 9. Routing Example

Imagine:

```text
PC
192.168.1.10
     |
     v
Router
192.168.1.1
     |
     +------ 10.10.10.0/24
     |
     +------ Internet
```

Routing table:

```text
Destination       Next Hop
192.168.1.0/24    Direct
10.10.10.0/24     192.168.1.1
0.0.0.0/0         192.168.1.1
```

If destination is:

```text
10.10.10.50
```

the host uses:

```text
10.10.10.0/24
```

If destination is:

```text
8.8.8.8
```

it uses:

```text
0.0.0.0/0
```

---

# 10. Viewing Routing Tables

## Linux / Kali

Modern Linux:

```bash
ip route
```

Example:

```text
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.20
```

Interpretation:

```text
default via 192.168.1.1
```

means:

```text
Default gateway = 192.168.1.1
```

And:

```text
192.168.1.0/24 dev eth0
```

means:

```text
192.168.1.0/24 is directly connected through eth0
```

---

# 11. Useful Linux Commands

### Show routes

```bash
ip route
```

### Show routing table in more detail

```bash
ip route show
```

### Show interfaces

```bash
ip addr
```

### Check which route Linux would use

```bash
ip route get 8.8.8.8
```

This is extremely useful for troubleshooting.

Example:

```text
8.8.8.8 via 192.168.1.1 dev eth0 src 192.168.1.20
```

Meaning:

```text
Destination → 8.8.8.8
Gateway     → 192.168.1.1
Interface   → eth0
Source IP   → 192.168.1.20
```

---

# 12. Windows Routing Table

Windows:

```cmd
route print
```

or:

```cmd
ipconfig
```

For PowerShell:

```powershell
Get-NetRoute
```

To inspect the route to a destination:

```cmd
tracert 8.8.8.8
```

---

# 13. Linux vs Windows

| Task                  | Linux/Kali        | Windows          |
| --------------------- | ----------------- | ---------------- |
| Show IP               | `ip addr`         | `ipconfig`       |
| Show routes           | `ip route`        | `route print`    |
| Route lookup          | `ip route get IP` | `tracert IP`     |
| Interface information | `ip link`         | `Get-NetAdapter` |
| Trace path            | `traceroute`      | `tracert`        |

---

# 14. Routing vs Switching

These are different concepts.

## Switching

Usually operates primarily at **Layer 2**.

```text
MAC Address
```

Example:

```text
PC → Switch → PC
```

## Routing

Operates at **Layer 3**.

```text
IP Address
```

Example:

```text
Network A → Router → Network B
```

### Easy Memory

```text
Switch → MAC
Router → IP
```

---

# 15. ARP and Routing

For IPv4, when a host needs to send traffic to another device on the local network, it may use **ARP** to discover the destination's MAC address.

Example:

```text
PC:
192.168.1.10

Target:
192.168.1.50
```

PC asks:

```text
Who has 192.168.1.50?
```

The target responds with its MAC address.

Then:

```text
IP packet
   ↓
Ethernet frame
   ↓
Destination MAC
```

---

# 16. What Happens When Destination Is Outside Local Network?

Suppose:

```text
PC = 192.168.1.10
Gateway = 192.168.1.1
Destination = 8.8.8.8
```

`8.8.8.8` is not on:

```text
192.168.1.0/24
```

So the PC sends the frame to the **gateway's MAC address**, while the IP packet's destination remains:

```text
8.8.8.8
```

Conceptually:

```text
IP destination:
8.8.8.8

Ethernet destination:
Router's MAC
```

This distinction is extremely important.

---

# 17. Routing Through Multiple Routers

Example:

```text
PC
 |
 v
Router A
 |
 v
Router B
 |
 v
Router C
 |
 v
Server
```

Each router makes a forwarding decision.

Router A:

```text
Where should I send the packet next?
```

Router B asks the same question.

Eventually:

```text
Server
```

is reached.

This is called **hop-by-hop forwarding**.

---

# 18. TTL and Routing

IPv4 packets contain a field called:

```text
TTL = Time To Live
```

It prevents packets from circulating forever.

Each router normally decreases TTL by 1.

Example:

```text
TTL 64
  ↓ Router
TTL 63
  ↓ Router
TTL 62
  ↓ Router
...
```

If TTL reaches zero, the packet is discarded.

This is also why tools such as:

```bash
traceroute
```

can discover intermediate hops.

---

# 19. Traceroute / Tracert

Linux:

```bash
traceroute 8.8.8.8
```

Windows:

```cmd
tracert 8.8.8.8
```

Conceptually:

```text
You
 ↓
Router 1
 ↓
Router 2
 ↓
Router 3
 ↓
Destination
```

### VAPT Relevance

Traceroute can help understand:

* Network topology
* Routing paths
* Network boundaries
* Possible filtering
* Segmentation
* Reachability

It should be used only against systems/networks you're authorized to assess.

---

# 20. Static vs Dynamic Routing

## Static Routing

Routes are manually configured.

Example:

```text
10.10.20.0/24 → 192.168.1.2
```

Advantages:

* Simple for small networks
* Predictable
* No routing protocol required

Disadvantages:

* Manual maintenance
* Doesn't automatically adapt to failures

---

# 21. Dynamic Routing

Routers learn routes using routing protocols.

Examples:

* OSPF
* BGP
* EIGRP
* IS-IS
* RIP

Dynamic routing is useful for larger networks.

---

# 22. OSPF

**OSPF = Open Shortest Path First**

It is commonly used as an interior routing protocol.

Basic idea:

```text
Router A ←→ Router B
   ↑           ↑
   └── Router C
```

Routers exchange topology information and calculate suitable paths.

### VAPT Relevance

Understanding routing protocols helps when assessing:

* Network segmentation
* Router configurations
* Route exposure
* Internal network architecture

---

# 23. BGP

**BGP = Border Gateway Protocol**

BGP is fundamental to routing between autonomous systems on the Internet.

Simplified:

```text
AS 100
   |
   | BGP
   |
AS 200
   |
   | BGP
   |
AS 300
```

BGP is much more advanced than the routing tables you normally inspect on a workstation.

---

# 24. Routing and VAPT

Routing knowledge is extremely important in VAPT.

A pentester needs to understand:

```text
Attacker
   |
   v
Network Boundary
   |
   v
Firewall
   |
   v
Internal Network
   |
   +---- Web Server
   |
   +---- Database
   |
   +---- Admin Network
```

The routing architecture helps determine:

* What is reachable?
* What is isolated?
* Which networks can communicate?
* Where are gateways?
* Where are segmentation boundaries?
* Is an internal service unintentionally exposed?

---

# 25. Network Segmentation

Good architecture:

```text
Internet
   |
 Firewall
   |
DMZ
 |
Web Server
 |
Firewall
 |
Internal Network
 |
Database
```

Poor segmentation might allow:

```text
Internet
   |
Web Server
   |
   +---- Database
   |
   +---- Admin Network
```

From a VAPT perspective, segmentation should be tested **within the authorized scope**.

---

# 26. Routing and Attack Surface

Suppose an assessment discovers:

```text
10.10.10.0/24
10.10.20.0/24
10.10.30.0/24
```

The important question isn't simply:

> "How many IPs exist?"

Instead:

> "Which networks are reachable from my current position?"

Routing information helps answer that.

---

# 27. Routing Table + Nmap

A common authorized lab workflow:

```text
1. Check local interface
        ↓
2. Check routing table
        ↓
3. Identify authorized target network
        ↓
4. Discover hosts
        ↓
5. Enumerate services
        ↓
6. Assess vulnerabilities
        ↓
7. Document findings
```

Example:

```bash
ip addr
ip route
```

Then, only for an authorized lab:

```bash
nmap <authorized-target>
```

---

# 28. Multiple Interfaces

A machine may have multiple network interfaces:

```text
eth0 → 192.168.1.20
tun0 → 10.10.10.5
```

This is common with:

* VPNs
* Virtual machines
* Containers
* Cloud environments

Routing determines which interface should be used for a destination.

---

# 29. VPN and Routing

A VPN often creates a virtual interface.

Example:

```text
tun0
10.10.10.5
```

Routing may contain:

```text
10.10.0.0/16 → tun0
```

Therefore traffic destined for:

```text
10.10.x.x
```

may travel through the VPN.

Conceptually:

```text
Your PC
  |
  +---- Normal Internet → eth0
  |
  +---- Private VPN Network → tun0
```

---

# 30. Virtual Machines and Routing

Kali inside VirtualBox/VMware may use:

### NAT

```text
Kali
 |
 v
Virtual NAT
 |
 v
Host
 |
 v
Internet
```

### Bridged

```text
Kali
 |
 v
Physical LAN
 |
 v
Router
```

### Host-only

```text
Kali ←→ Host
```

The networking mode changes what the VM can reach.

---

# 31. Common Routing Problems

## Problem 1: No Default Route

```text
ip route
```

doesn't show:

```text
default via ...
```

Internet connectivity may fail.

---

## Problem 2: Wrong Gateway

Example:

```text
PC = 192.168.1.20
Gateway = 192.168.2.1
```

Depending on the network configuration, this may be invalid/unreachable.

---

## Problem 3: Wrong Subnet Mask

Example:

```text
IP: 192.168.1.20/24
```

vs incorrectly configured:

```text
192.168.1.20/16
```

This can change which destinations the host considers directly connected.

---

## Problem 4: Conflicting Routes

Multiple routes may match the same destination.

You need to understand:

```text
Prefix specificity
+
Route preference/metric
```

---

# 32. Routing vs NAT

Don't confuse these.

### Routing

Determines:

```text
Where should the packet go?
```

### NAT

Changes address information, typically IP addresses and sometimes ports.

Example:

```text
Private:
192.168.1.20

NAT Router

Public:
203.0.113.x
```

Routing and NAT often work together, but they are different functions.

---

# 33. Important Route Types

You may encounter:

```text
Connected route
Static route
Default route
Dynamic route
Host route
Network route
```

A host route is commonly represented using:

```text
/32
```

Example:

```text
192.168.1.50/32
```

This identifies exactly one IPv4 address.

---

# 34. IPv6 Default Route

IPv6 uses:

```text
::/0
```

instead of:

```text
0.0.0.0/0
```

Example:

```text
default via fe80::1 dev eth0
```

IPv6 routing also has important concepts such as:

* Neighbor Discovery
* Router Advertisements
* Link-local addresses
* SLAAC

---

# 35. Routing Mental Model

Whenever you see an IP destination, ask:

```text
1. Is it local?
        |
        +-- YES → Direct delivery
        |
        +-- NO
             ↓
       Check routing table
             ↓
       Find matching routes
             ↓
       Longest-prefix match
             ↓
       Determine next hop
             ↓
       Select interface
             ↓
       Forward packet
```

This is the **core mental model**.

---

# 36. Practical Lab — Routing Table

Use your own Kali VM or authorized lab.

### Step 1 — Find your IP

```bash
ip addr
```

### Step 2 — Find your routes

```bash
ip route
```

### Step 3 — Check default gateway

Look for:

```text
default via X.X.X.X
```

### Step 4 — Ask Linux how it would route traffic

```bash
ip route get 8.8.8.8
```

### Step 5 — Trace the path

```bash
traceroute 8.8.8.8
```

If `traceroute` isn't installed, use the appropriate package available in your environment.

---

# 37. Practical Lab Questions

After running the commands, answer:

### Q1

What is your IP address?

### Q2

What is your subnet/prefix?

### Q3

What is your default gateway?

### Q4

Which interface provides Internet connectivity?

### Q5

What route is selected for `8.8.8.8`?

### Q6

Does your machine have more than one interface?

### Q7

Do you have any VPN routes?

---

# 38. Scenario-Based Questions

### Scenario 1

Your machine has:

```text
192.168.1.10/24
Gateway: 192.168.1.1
```

Destination:

```text
192.168.1.50
```

Does the packet need the default gateway?

**Answer:** Normally no. The destination is within the directly connected `192.168.1.0/24` network.

---

### Scenario 2

Destination:

```text
8.8.8.8
```

Does the local `/24` route match?

**Answer:** No.

The default route is normally used.

---

### Scenario 3

Routing table:

```text
10.0.0.0/8
10.10.0.0/16
10.10.20.0/24
```

Destination:

```text
10.10.20.55
```

Which route wins?

**Answer:**

```text
10.10.20.0/24
```

because it is the longest/more-specific matching prefix.

---

# 39. Interview Questions

### Beginner

**Q1. What is routing?**

Routing is the process of determining how packets reach their destination.

**Q2. What is a routing table?**

A collection of routes used to make packet-forwarding decisions.

**Q3. What is a default gateway?**

The next-hop router normally used when no more specific route matches.

**Q4. What is a default IPv4 route?**

```text
0.0.0.0/0
```

**Q5. What is an IPv6 default route?**

```text
::/0
```

---

### Intermediate

**Q6. What is longest-prefix matching?**

Selecting the most specific matching route for a destination.

**Q7. Difference between routing and switching?**

Routing forwards between IP networks; switching primarily forwards frames within a Layer-2 network.

**Q8. Why is TTL important?**

It prevents packets from circulating indefinitely.

**Q9. What command displays routes in Linux?**

```bash
ip route
```

**Q10. What command displays routes in Windows?**

```cmd
route print
```

---

# 40. Advanced Interview Questions

### Q1. Why can multiple routes match one destination?

Because routing tables can contain networks of different prefix lengths.

Example:

```text
10.0.0.0/8
10.10.0.0/16
10.10.20.0/24
```

### Q2. What determines the selected route?

In simplified terms:

```text
Longest prefix match
→ then applicable route preference/metric rules
```

The exact selection process varies by operating system and routing implementation.

### Q3. Why can a host have multiple interfaces?

Examples:

* Ethernet
* Wi-Fi
* VPN
* Virtual machines
* Containers

The routing table determines which path/interface is appropriate.

### Q4. How does routing help VAPT?

It helps a tester understand:

```text
Reachability
Segmentation
Network boundaries
Attack surface
VPN paths
Internal architecture
```

---

# 41. MCQs

### 1. What is the purpose of a routing table?

A. Store passwords
B. Decide packet forwarding paths
C. Store DNS records
D. Encrypt traffic

**Answer: B**

---

### 2. What is the IPv4 default route?

A. `127.0.0.1`
B. `255.255.255.255`
C. `0.0.0.0/0`
D. `192.168.1.1/24`

**Answer: C**

---

### 3. Which command displays routes on Linux?

A. `ip route`
B. `show ip`
C. `route-ip`
D. `net route`

**Answer: A**

---

### 4. Which command displays the Windows routing table?

A. `ip route`
B. `route print`
C. `show route`
D. `routing`

**Answer: B**

---

### 5. Which route is more specific?

A. `/8`
B. `/16`
C. `/24`
D. `/0`

**Answer: C**

---

### 6. What does `::/0` represent?

A. IPv4 localhost
B. IPv6 default route
C. IPv6 loopback only
D. IPv4 broadcast

**Answer: B**

---

### 7. Which device primarily performs Layer-3 routing?

A. Hub
B. Switch
C. Router
D. Repeater

**Answer: C**

---

### 8. What does TTL help prevent?

A. DNS failures
B. Infinite packet loops
C. Password attacks
D. Port scanning

**Answer: B**

---

### 9. What does `ip route get 8.8.8.8` help determine?

A. Password
B. Selected route/path decision
C. DNS password
D. MAC vendor

**Answer: B**

---

### 10. Which protocol is associated with routing between autonomous systems?

A. HTTP
B. FTP
C. BGP
D. ARP

**Answer: C**

---

# 42. Common Beginner Mistakes

### ❌ Mistake 1

Thinking the default gateway is used for **every** packet.

### ✅ Correct

Directly connected destinations normally don't need the default gateway.

---

### ❌ Mistake 2

Thinking routing and NAT are the same.

### ✅ Correct

Routing decides forwarding; NAT translates addresses/ports.

---

### ❌ Mistake 3

Thinking routers forward based on MAC addresses between networks.

### ✅ Correct

Routers make Layer-3 forwarding decisions using IP information.

---

### ❌ Mistake 4

Memorizing routes without understanding prefixes.

### ✅ Correct

Understand:

```text
IP + Prefix
      ↓
Network
      ↓
Route matching
```

---

# 43. Must-Memorize Cheat Sheet

```text
Routing
    ↓
Decides where packets go

Routing Table
    ↓
Collection of forwarding rules

Default IPv4 Route
    ↓
0.0.0.0/0

Default IPv6 Route
    ↓
::/0

Linux
    ↓
ip route

Windows
    ↓
route print

Most specific route
    ↓
Longest prefix match

Switch
    ↓
Primarily Layer 2 / MAC

Router
    ↓
Layer 3 / IP

IPv4 local resolution
    ↓
ARP

IPv4 TTL
    ↓
Decreases across routed hops
```

---

# 44. Key Takeaways

* Routing determines how packets reach destinations.
* A routing table contains forwarding rules.
* The default route is the fallback route.
* IPv4 default route = `0.0.0.0/0`.
* IPv6 default route = `::/0`.
* More-specific routes generally win through longest-prefix matching.
* Linux uses `ip route` to inspect routes.
* Windows uses `route print`.
* Routers connect different IP networks.
* Routing is different from NAT.
* TTL helps prevent endless packet loops.
* VPNs and virtual machines often add interfaces and routes.
* Routing knowledge is essential for understanding network reachability and segmentation during authorized VAPT.

---

# 45. Final Mental Model

Don't memorize routing as a collection of commands.

Think:

```text
             DESTINATION
                  |
                  v
        ┌──────────────────┐
        │ Routing Table    │
        └────────┬─────────┘
                 |
                 v
       Which routes match?
                 |
                 v
       Longest prefix match
                 |
                 v
           Next Hop
                 |
                 v
          Network Interface
                 |
                 v
              PACKET
                 |
                 v
           Next Router
                 |
                 v
           DESTINATION
```

### The one question to remember:

> **"For this destination IP, which route will my machine choose, through which next hop, and through which interface?"**

If you can answer that confidently using `ip route`, prefixes, gateways, and interfaces, you have the foundation needed for **intermediate networking + VAPT enumeration**.
