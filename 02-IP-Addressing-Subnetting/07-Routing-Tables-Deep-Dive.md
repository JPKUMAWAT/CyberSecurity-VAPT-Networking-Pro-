# 07 – Routing Tables Deep Dive

> **Goal:** Understand how Linux and network devices decide where packets go.
>
> **Level:** 🟢 Beginner → 🟡 Intermediate → 🔴 VAPT
>
> **Cybersecurity relevance:** ⭐⭐⭐⭐⭐
>
> **Prerequisite:** IPv4, CIDR, Subnetting, TCP/IP basics

---

# 1. What Is Routing?

**Routing** is the process of deciding **where a network packet should be sent next**.

Imagine sending a parcel:

```text
You
 ↓
Local Road
 ↓
Main Road
 ↓
Highway
 ↓
Destination
```

Networking works similarly:

```text
Your Computer
     ↓
Default Gateway
     ↓
Router
     ↓
Internet
     ↓
Destination Server
```

A device uses its **routing table** to make this decision.

### Simple definition

> **Routing table = A set of rules that tells a device where to send packets.**

---

# 2. Why Routing Is Important

Without routing, a device would not know:

* Where another network is located
* Which gateway to use
* Which interface should send traffic
* Whether the destination is local or remote
* Where Internet traffic should go

For VAPT, routing knowledge helps you understand:

* Network architecture
* Internal networks
* VPN routes
* Pivoting
* Segmentation
* Lateral movement
* Firewall/network restrictions
* Multi-interface systems

---

# 3. What Is a Routing Table?

A routing table contains information such as:

| Destination    | Gateway     | Interface | Metric |
| -------------- | ----------- | --------- | -----: |
| 192.168.1.0/24 | Direct      | eth0      |    100 |
| 10.10.10.0/24  | 192.168.1.1 | eth0      |    100 |
| 0.0.0.0/0      | 192.168.1.1 | eth0      |    100 |

Think of it as a **map**.

```text
Destination
     ↓
Which route?
     ↓
Which gateway?
     ↓
Which interface?
     ↓
Send packet
```

---

# 4. Important Routing Terms

## 4.1 Destination

The network or host where the packet needs to go.

Example:

```text
192.168.1.0/24
```

means:

```text
192.168.1.0 – 192.168.1.255
```

---

## 4.2 Gateway

A gateway is the next device that forwards traffic toward another network.

Example:

```text
PC
 |
 | 192.168.1.10
 |
Gateway
192.168.1.1
 |
Internet
```

Usually your home router acts as the default gateway.

---

# 5. Default Gateway

The **default gateway** is used when there is no more specific route available.

Example:

```text
Destination: 8.8.8.8
```

Your computer checks the routing table.

If it doesn't have a specific route for `8.8.8.8`, it uses:

```text
0.0.0.0/0
```

This is the **default route**.

---

# 6. Default Route

The most important default route is:

```text
0.0.0.0/0
```

Meaning:

> "If no better route exists, send traffic here."

Example:

```text
default via 192.168.1.1 dev eth0
```

Interpretation:

```text
Default traffic
      ↓
Gateway 192.168.1.1
      ↓
Interface eth0
```

---

# 7. Network Interface

A network interface is the connection through which traffic leaves or enters a system.

Examples:

```text
eth0
wlan0
ens33
tun0
lo
```

Common examples:

| Interface | Typical purpose                |
| --------- | ------------------------------ |
| eth0      | Ethernet                       |
| wlan0     | Wi-Fi                          |
| lo        | Loopback                       |
| tun0      | VPN/tunnel                     |
| ens33     | Ethernet on some Linux systems |

Interface names can vary depending on the system.

---

# 8. Linux Routing Commands

The modern command you should learn first is:

```bash
ip route
```

Example:

```text
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.10
```

---

# 9. Understanding `ip route`

Run:

```bash
ip route
```

You might see:

```text
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.10
```

Let's understand each part.

---

## Route 1

```text
default via 192.168.1.1 dev eth0
```

Means:

```text
Default route
     ↓
Gateway = 192.168.1.1
     ↓
Interface = eth0
```

---

## Route 2

```text
192.168.1.0/24 dev eth0
```

Means:

```text
Destination network = 192.168.1.0/24
Interface = eth0
```

Because this network is directly connected, a gateway isn't necessary.

---

# 10. Directly Connected Network

Suppose your machine has:

```text
IP:      192.168.1.10
Mask:    255.255.255.0
```

CIDR:

```text
192.168.1.10/24
```

Network:

```text
192.168.1.0/24
```

Your machine can directly communicate with:

```text
192.168.1.x
```

without sending the traffic through the default gateway.

Example:

```text
PC
192.168.1.10
   |
   | Direct
   |
192.168.1.20
```

---

# 11. Local vs Remote Destination

Suppose:

```text
Your IP:
192.168.1.10/24
```

### Destination 1

```text
192.168.1.20
```

Same network.

Therefore:

```text
PC → Directly to destination
```

### Destination 2

```text
8.8.8.8
```

Different network.

Therefore:

```text
PC
 ↓
Default Gateway
 ↓
Internet
 ↓
8.8.8.8
```

---

# 12. How Does a Computer Choose a Route?

This is one of the **most important routing concepts**.

Suppose the routing table contains:

```text
10.0.0.0/8
10.10.0.0/16
10.10.10.0/24
0.0.0.0/0
```

Destination:

```text
10.10.10.50
```

Which route is selected?

Answer:

```text
10.10.10.0/24
```

Why?

Because it is the **most specific matching route**.

---

# 13. Longest Prefix Match

Routers generally choose the route with the **longest matching prefix**.

Example:

```text
10.0.0.0/8
10.10.0.0/16
10.10.10.0/24
```

Destination:

```text
10.10.10.50
```

All three may match.

But:

```text
/24 > /16 > /8
```

Therefore:

```text
10.10.10.0/24
```

wins.

### Remember:

> **More specific route = preferred over less specific matching route.**

This is extremely important for networking interviews.

---

# 14. Routing Example

Imagine:

```text
PC
192.168.1.10
   |
   | eth0
   |
Router
192.168.1.1
   |
   +------ 10.10.10.0/24
   |
   +------ Internet
```

Routing table:

```text
192.168.1.0/24 dev eth0
10.10.10.0/24 via 192.168.1.1
default via 192.168.1.1
```

Traffic to:

```text
10.10.10.50
```

goes:

```text
PC
 ↓
192.168.1.1
 ↓
10.10.10.50
```

---

# 15. `ip route get`

One of the most useful commands for troubleshooting:

```bash
ip route get 8.8.8.8
```

It tells you which route Linux would use for that destination.

Example:

```text
8.8.8.8 via 192.168.1.1 dev eth0 src 192.168.1.10
```

Meaning:

```text
Destination → 8.8.8.8
Gateway     → 192.168.1.1
Interface   → eth0
Source IP   → 192.168.1.10
```

### VAPT value

Very useful when troubleshooting:

* VPNs
* Multiple interfaces
* Lab networks
* Pivoting
* Routing problems

---

# 16. Viewing Interfaces

Use:

```bash
ip addr
```

or:

```bash
ip a
```

Example:

```text
2: eth0:
    inet 192.168.1.10/24
```

This tells you:

```text
Interface = eth0
IP = 192.168.1.10
Network = /24
```

---

# 17. Viewing Routing Information

Basic:

```bash
ip route
```

More detailed:

```bash
ip -4 route
```

IPv6:

```bash
ip -6 route
```

---

# 18. Adding a Route

In a controlled lab, you can add a route.

Example:

```bash
sudo ip route add 10.10.10.0/24 via 192.168.1.1
```

Meaning:

```text
To reach 10.10.10.0/24
use gateway 192.168.1.1
```

### Important

Only modify routing tables on systems/networks you own or are authorized to administer.

---

# 19. Removing a Route

Example:

```bash
sudo ip route del 10.10.10.0/24
```

Verify:

```bash
ip route
```

---

# 20. Routing Metrics

Sometimes multiple routes can reach the same destination.

A **metric** helps determine which route is preferred.

Example:

```text
10.10.10.0/24 via 192.168.1.1 metric 100
10.10.10.0/24 via 192.168.1.2 metric 200
```

Usually the lower metric is preferred when otherwise comparable.

Think:

```text
Lower metric
     ↓
Usually preferred
```

But route selection also depends on prefix specificity and other routing rules.

---

# 21. Routing vs Switching

Very important distinction.

## Switching

Usually operates within the local network.

```text
PC → Switch → PC
```

## Routing

Connects different networks.

```text
Network A
   ↓
Router
   ↓
Network B
```

Simple memory:

> **Switch = same/local network forwarding**
>
> **Router = between networks**

---

# 22. ARP and Routing

For IPv4 local-network communication, the system needs the destination's MAC address.

Example:

```text
192.168.1.10
      ↓
Need to communicate with
      ↓
192.168.1.20
```

ARP can resolve:

```text
IP → MAC
```

Check ARP/neighbour information:

```bash
ip neigh
```

Example:

```text
192.168.1.1 dev eth0 lladdr xx:xx:xx:xx:xx:xx REACHABLE
```

---

# 23. Routing + ARP Together

Suppose:

```text
Your PC:
192.168.1.10

Destination:
192.168.1.20
```

Same subnet.

Process:

```text
1. Check routing table
        ↓
2. Destination is local
        ↓
3. Find MAC using ARP
        ↓
4. Send Ethernet frame
```

For an Internet destination:

```text
1. Check routing table
        ↓
2. Destination is remote
        ↓
3. Select default gateway
        ↓
4. Find gateway MAC
        ↓
5. Send frame to gateway
```

---

# 24. Routing and VPNs

VPNs commonly create a virtual interface.

Example:

```text
tun0
```

You might see:

```text
10.10.0.0/16 dev tun0
```

This means traffic for:

```text
10.10.x.x
```

should go through:

```text
tun0
```

This is extremely common in cybersecurity labs.

Example:

```text
Your machine
     |
     +--- eth0 → Internet
     |
     +--- tun0 → VPN lab
```

---

# 25. Why VAPT Professionals Care About Routes

During an assessment, you may encounter:

```text
eth0
tun0
docker0
lo
```

Each may represent a different network.

Understanding routes helps answer:

* Which network am I connected to?
* Where will this traffic go?
* Is the target reachable?
* Is the VPN route installed?
* Which interface will be used?
* Why is my connection failing?

---

# 26. Routing and Network Segmentation

Suppose an organization has:

```text
User Network
192.168.10.0/24

Server Network
192.168.20.0/24

Database Network
192.168.30.0/24
```

A tester may discover:

```text
192.168.10.0/24 → reachable
192.168.20.0/24 → reachable
192.168.30.0/24 → blocked
```

This can indicate:

* Firewall rules
* Routing restrictions
* Network segmentation
* Access-control policies

Routing information alone does **not** prove a firewall exists; it is one piece of the investigation.

---

# 27. Routing in a VAPT Workflow

A basic network assessment workflow might look like:

```text
Identify Interface
       ↓
Identify IP Address
       ↓
Check Routing Table
       ↓
Identify Reachable Networks
       ↓
Discover Authorized Hosts
       ↓
Enumerate Services
       ↓
Assess Security
       ↓
Document Findings
```

Useful commands:

```bash
ip a
ip route
ip route get <destination>
ip neigh
```

---

# 28. Troubleshooting Example

Suppose you cannot reach:

```text
10.10.10.20
```

Start with:

```bash
ip a
```

Check your IP.

Then:

```bash
ip route
```

Ask:

> Is there a route toward `10.10.10.0/24`?

Then:

```bash
ip route get 10.10.10.20
```

Check which interface and gateway Linux selects.

Then test connectivity in your authorized lab:

```bash
ping -c 4 10.10.10.20
```

If appropriate, investigate the path:

```bash
traceroute 10.10.10.20
```

---

# 29. Traceroute

`traceroute` helps show the path packets take toward a destination.

Example:

```bash
traceroute example.com
```

On some systems:

```bash
tracepath example.com
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

### Important

Traceroute results can be incomplete because routers or firewalls may filter or rate-limit the relevant packets.

---

# 30. Routing in Windows

Windows uses:

```cmd
route print
```

or PowerShell:

```powershell
Get-NetRoute
```

To inspect IP configuration:

```cmd
ipconfig
```

So:

### Linux

```bash
ip route
```

### Windows

```cmd
route print
```

---

# 31. Static vs Dynamic Routing

## Static Routing

Routes are manually configured.

Example:

```text
Network A → Router 1
```

Advantages:

* Simple for small networks
* Predictable
* No routing protocol required

Disadvantages:

* Difficult to manage at scale
* Manual changes required

---

## Dynamic Routing

Routers exchange routing information using routing protocols.

Examples:

* OSPF
* BGP
* EIGRP
* RIP

Dynamic routing is common in larger networks.

---

# 32. Routing Protocols

You should recognize these:

| Protocol | Common use                                      |
| -------- | ----------------------------------------------- |
| OSPF     | Internal enterprise routing                     |
| BGP      | Internet / inter-domain routing                 |
| RIP      | Older/simple routing                            |
| EIGRP    | Historically associated with Cisco environments |

For a beginner VAPT learner, understand their **purpose** before trying to master their configuration.

---

# 33. Routing Table vs ARP Table

Don't confuse them.

### Routing table

Answers:

> **Where should I send the packet?**

Command:

```bash
ip route
```

### ARP/Neighbour table

Answers:

> **What MAC address corresponds to this local IP?**

Command:

```bash
ip neigh
```

Memory:

```text
Routing:
IP → Next path

ARP:
IP → MAC
```

---

# 34. Routing Table vs DNS

Also don't confuse these.

### DNS

Converts:

```text
example.com
     ↓
IP address
```

### Routing

Determines:

```text
IP address
     ↓
Where should the packet go?
```

Complete flow:

```text
example.com
     ↓
DNS
     ↓
IP address
     ↓
Routing table
     ↓
Gateway/interface
     ↓
Network
```

---

# 35. Important VAPT Concepts Connected to Routing

Learn these after mastering basic routes:

### 1. Network segmentation

Separating networks to limit access.

### 2. VPN routing

Routes traffic through encrypted tunnels.

### 3. Pivoting

Using an authorized intermediate system to reach another network.

### 4. Multi-homing

A system has multiple network interfaces.

### 5. Routing misconfiguration

Incorrect routes can expose or isolate networks unexpectedly.

### 6. Asymmetric routing

Traffic travels through different paths in different directions.

---

# 36. Mini Practical Lab

Use your own Kali VM or an authorized lab.

## Step 1 — Find your IP

```bash
ip a
```

Record:

```text
IP:
Subnet:
Interface:
```

---

## Step 2 — Check routes

```bash
ip route
```

Identify:

```text
Default gateway:
Default interface:
Local network:
```

---

## Step 3 — Check a destination route

```bash
ip route get 8.8.8.8
```

Record:

```text
Gateway:
Interface:
Source IP:
```

---

## Step 4 — Check neighbours

```bash
ip neigh
```

Look for your gateway.

---

## Step 5 — Trace a route

On your authorized network:

```bash
traceroute 8.8.8.8
```

or:

```bash
tracepath 8.8.8.8
```

---

# 37. VAPT Practical Exercise

Create a small network:

```text
Kali VM
192.168.56.10
      |
      |
Virtual Network
      |
      |
Target VM
192.168.56.20
```

On Kali:

```bash
ip a
ip route
ip neigh
```

Then determine:

```text
1. Kali IP
2. Network/subnet
3. Interface
4. Route to target
5. Target MAC address
```

This teaches you to **read the network before scanning it**.

---

# 38. Advanced Concept: Multiple Interfaces

Imagine:

```text
eth0 → 192.168.1.10
tun0 → 10.10.10.5
```

Routing decides:

```text
192.168.1.x → eth0

10.10.0.0/16 → tun0

Internet → eth0
```

This is why a tool may work through one interface but fail through another.

---

# 39. Advanced Concept: Source Address Selection

When a system has multiple interfaces, it may need to choose a source IP.

Example:

```text
eth0 = 192.168.1.10
tun0 = 10.10.10.5
```

A route can influence which source address is appropriate.

Check with:

```bash
ip route get <destination>
```

Look for:

```text
src <IP>
```

This is very useful for debugging VPN and multi-interface environments.

---

# 40. Common Mistakes

### ❌ Mistake 1

Thinking:

```text
Gateway = DNS server
```

Not necessarily.

Gateway and DNS server perform different functions.

---

### ❌ Mistake 2

Thinking every destination goes through the gateway.

Not necessarily.

Local destinations may be directly reachable.

---

### ❌ Mistake 3

Thinking routing determines whether a port is open.

No.

Routing determines the path.

Port/service state is a separate issue.

---

### ❌ Mistake 4

Confusing:

```text
Routing table
```

with:

```text
ARP table
```

---

### ❌ Mistake 5

Assuming traceroute always shows every router.

It may not.

---

# 41. Interview Questions

## Q1. What is routing?

Routing is the process of determining the path used to deliver packets between networks.

---

## Q2. What is a routing table?

A routing table contains rules that tell a system where to forward traffic for different destinations.

---

## Q3. What is a default route?

A route used when no more specific matching route exists.

Usually represented as:

```text
0.0.0.0/0
```

for IPv4.

---

## Q4. What is a gateway?

A next-hop device used to reach another network.

---

## Q5. What command displays routes in Linux?

```bash
ip route
```

---

## Q6. What does `ip route get` do?

It shows the route Linux would use to reach a particular destination.

---

## Q7. What is longest-prefix match?

The most specific matching route is generally selected.

---

## Q8. Difference between routing and switching?

Switching primarily forwards traffic within a local network, while routing forwards traffic between networks.

---

## Q9. What is a routing metric?

A value used to help select between comparable routes.

---

## Q10. What is a VPN route?

A route that directs traffic through a VPN/tunnel interface.

---

# 42. Quick Command Cheat Sheet

| Purpose                    | Command                 |
| -------------------------- | ----------------------- |
| Show IP addresses          | `ip a`                  |
| Show IPv4 routes           | `ip -4 route`           |
| Show IPv6 routes           | `ip -6 route`           |
| Show routing table         | `ip route`              |
| Check route to destination | `ip route get <IP>`     |
| Show neighbour table       | `ip neigh`              |
| Trace path                 | `traceroute <IP>`       |
| Alternative trace          | `tracepath <IP>`        |
| Add route                  | `sudo ip route add ...` |
| Delete route               | `sudo ip route del ...` |

---

# 43. Must-Remember Concepts

```text
Routing
   ↓
Chooses path

Routing Table
   ↓
Contains routes

Gateway
   ↓
Next-hop device

Default Route
   ↓
0.0.0.0/0

Interface
   ↓
Where traffic leaves/enters

Longest Prefix Match
   ↓
Most specific route wins

ARP
   ↓
IPv4 → MAC on local network

VPN
   ↓
Often creates a virtual interface + routes
```

---

# 44. Final Mental Model

When you send:

```text
ping 8.8.8.8
```

Think:

```text
Application
    ↓
Destination IP = 8.8.8.8
    ↓
Routing table checked
    ↓
No specific route?
    ↓
Default route
    ↓
Gateway
    ↓
Network interface
    ↓
Local network
    ↓
Router(s)
    ↓
Internet
    ↓
8.8.8.8
```

This is the mental model you should carry into:

* Nmap
* Wireshark
* VPN labs
* Network VAPT
* Pivoting
* Firewall testing
* Troubleshooting
* Red-team labs

---

# 45. Mastery Checklist

Before moving to the next topic, you should be able to explain without notes:

* [ ] What routing means
* [ ] What a routing table is
* [ ] What a gateway does
* [ ] What `0.0.0.0/0` means
* [ ] What an interface is
* [ ] Local vs remote destination
* [ ] Longest-prefix matching
* [ ] Routing metrics
* [ ] Routing vs switching
* [ ] Routing vs DNS
* [ ] Routing vs ARP
* [ ] VPN routing
* [ ] Multi-interface routing
* [ ] `ip route`
* [ ] `ip route get`
* [ ] `ip neigh`
* [ ] `traceroute`
* [ ] Basic static routes

---

# One-Line Revision

> **Routing answers one critical question: "For this destination IP, which route, gateway, and interface should I use to send the packet?"**
