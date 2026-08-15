

# 1. What Is DNS?

**DNS = Domain Name System**

DNS is the system that translates human-readable domain names into IP addresses.

For example:

```text
www.example.com
       ↓
DNS
       ↓
93.184.216.34
```

Humans prefer:

```text
google.com
```

Computers communicate using:

```text
IP addresses
```

DNS acts like the **Internet's phonebook**.

### Simple Example

When you type:

```text
https://example.com
```

your computer needs to discover the server's IP address before establishing the connection.

---

# 2. Why DNS Is Important in VAPT

DNS is one of the first places a security tester studies during reconnaissance.

DNS can reveal:

* IP addresses
* Subdomains
* Mail servers
* Name servers
* DNS infrastructure
* Internal naming patterns
* Cloud services
* Potentially forgotten assets

### Example

Suppose a company owns:

```text
example.com
```

DNS may reveal:

```text
www.example.com
mail.example.com
vpn.example.com
dev.example.com
api.example.com
```

Each discovered hostname may represent a different attack surface.

---

# 3. DNS Basic Architecture

A simplified DNS lookup:

```text
User
 │
 │ "What is the IP of example.com?"
 ▼
Local DNS Resolver
 │
 ▼
Root DNS Server
 │
 ▼
TLD DNS Server (.com)
 │
 ▼
Authoritative DNS Server
 │
 ▼
IP Address
 │
 ▼
User
```

For many normal lookups, the resolver handles this process on behalf of the client.

---

# 4. Important DNS Components

There are four major components you should understand.

## 4.1 DNS Client / Stub Resolver

The device that needs DNS information.

Examples:

* Laptop
* Phone
* Server

It sends a DNS query to a configured resolver.

---

## 4.2 Recursive Resolver

A DNS resolver receives the client's request and finds the answer.

Examples include:

```text
ISP DNS resolver
Enterprise DNS resolver
Public DNS resolver
```

Common public resolvers include:

```text
8.8.8.8
1.1.1.1
```

The resolver may cache previous answers.

---

## 4.3 Root DNS Servers

The DNS hierarchy begins at the root.

Root servers don't normally provide the final IP address.

Instead, they can direct resolvers toward the appropriate TLD servers.

Example:

```text
example.com
       ↓
Root
       ↓
.com TLD
```

---

## 4.4 Authoritative DNS Server

The authoritative server contains the DNS records for a domain/zone.

For example:

```text
example.com
```

might have authoritative DNS servers such as:

```text
ns1.example-dns.com
ns2.example-dns.com
```

These servers provide authoritative answers for the zone.

---

# 5. DNS Hierarchy

DNS uses a hierarchical structure.

```text
                    .
                  Root
                   |
              +----+----+
             .com      .org
               |
           example
               |
              www
```

Consider:

```text
www.example.com
```

Break it down:

```text
www       → hostname/subdomain label
example   → domain
.com      → TLD
.         → root
```

---

# 6. What Is a DNS Query?

A DNS query is a request asking for information about a domain.

Example:

```text
What is the A record for example.com?
```

The DNS server returns a response containing the requested information if available.

---

# 7. DNS Query Flow

Example:

```text
Browser
   |
   | DNS query
   ↓
DNS Resolver
   |
   | lookup
   ↓
Authoritative DNS
   |
   | answer
   ↓
DNS Resolver
   |
   | IP
   ↓
Browser
```

Then the browser can connect to the returned IP.

---

# 8. Recursive vs Iterative Queries

This is an important interview concept.

## Recursive Query

The client asks the resolver:

> "Find the final answer for me."

Example:

```text
Client → Resolver
         ↓
       Root
         ↓
       TLD
         ↓
   Authoritative
         ↓
       Answer
```

The resolver performs the work.

---

## Iterative Query

The server responds with the best information it currently has, potentially referring the requester to another DNS server.

Example:

```text
Resolver → Root
Root → "Ask the .com server"
Resolver → .com
.com → "Ask authoritative server"
Resolver → Authoritative
Authoritative → IP
```

### Easy Memory Trick

```text
Recursive = "You find it for me."

Iterative = "Tell me where I should look next."
```

---

# 9. DNS Record Types

You MUST know these.

| Record | Purpose                    |
| ------ | -------------------------- |
| A      | Domain → IPv4              |
| AAAA   | Domain → IPv6              |
| CNAME  | Alias to another hostname  |
| MX     | Mail server                |
| NS     | Authoritative name server  |
| TXT    | Text/policy information    |
| PTR    | Reverse DNS                |
| SOA    | Zone authority information |
| SRV    | Service location           |

---

# 10. A Record

An **A record** maps a hostname to an IPv4 address.

Example:

```text
example.com → 93.184.216.34
```

Conceptually:

```text
example.com
     ↓
IPv4
```

### Query

Linux/Kali:

```bash
dig example.com A
```

or:

```bash
host -t A example.com
```

Windows:

```cmd
nslookup -type=A example.com
```

---

# 11. AAAA Record

AAAA records map hostnames to IPv6 addresses.

Example:

```text
example.com
     ↓
IPv6 address
```

Query:

```bash
dig example.com AAAA
```

### Remember

```text
A     → IPv4
AAAA  → IPv6
```

---

# 12. CNAME Record

CNAME means:

**Canonical Name**

It creates an alias for another hostname.

Example:

```text
blog.example.com
       ↓
CNAME
       ↓
example.github.io
```

The CNAME points to another hostname rather than directly representing an IP address.

### VAPT relevance

CNAME records can reveal:

* Cloud services
* SaaS providers
* CDN infrastructure
* Third-party hosting

They can also be relevant when investigating **subdomain takeover** conditions, although a CNAME alone does **not** prove a takeover vulnerability.

---

# 13. MX Record

MX = **Mail Exchange**

It specifies mail servers responsible for receiving email for a domain.

Example:

```text
example.com
      ↓
MX
      ↓
mail.example.com
```

Query:

```bash
dig example.com MX
```

### VAPT relevance

MX records can help identify:

* Email infrastructure
* Third-party mail providers
* Security gateways
* External services

---

# 14. NS Record

NS = **Name Server**

It identifies authoritative DNS servers.

Example:

```text
example.com
     ↓
NS
     ↓
ns1.example.com
ns2.example.com
```

Query:

```bash
dig example.com NS
```

### VAPT relevance

Useful during DNS reconnaissance.

---

# 15. TXT Record

TXT records store text information.

They are commonly used for:

* SPF
* Domain verification
* Email security policies
* Service verification

Example:

```text
example.com TXT
```

Query:

```bash
dig example.com TXT
```

### Security relevance

TXT records may reveal information about:

```text
Email security
Cloud services
Domain ownership
Third-party integrations
```

Do not assume every TXT record contains sensitive information.

---

# 16. PTR Record

PTR performs reverse DNS.

Normal DNS:

```text
hostname → IP
```

Reverse DNS:

```text
IP → hostname
```

Example:

```text
8.8.8.8
   ↓
dns.google
```

Query:

```bash
dig -x 8.8.8.8
```

### Memory

```text
A record   → name → IPv4
PTR record → IPv4 → name
```

---

# 17. SOA Record

SOA = **Start of Authority**

It contains important zone information.

It can include:

* Primary authoritative name server
* Responsible administrative contact
* Serial number
* Refresh interval
* Retry interval
* Expiration
* Minimum/negative caching information

Query:

```bash
dig example.com SOA
```

You don't need to memorize every field initially.

Understand its purpose:

> **SOA describes authority and operational information for a DNS zone.**

---

# 18. SRV Record

SRV records specify the location of certain services.

Conceptually:

```text
_service._protocol.example.com
```

They can provide:

* Target hostname
* Port
* Priority
* Weight

Example uses include:

```text
VoIP
LDAP
Kerberos
Microsoft services
```

Query:

```bash
dig _sip._tcp.example.com SRV
```

---

# 19. DNS Port

DNS commonly uses:

```text
UDP 53
```

TCP is also used.

### UDP 53

Common for normal DNS queries because it has low overhead.

### TCP 53

Used in situations such as:

* Zone transfers
* Responses requiring TCP
* DNS operations where TCP is necessary

### Important

Don't memorize:

> "DNS always uses UDP."

Correct understanding:

```text
DNS → UDP and TCP
```

---

# 20. DNS Message Structure

A DNS message broadly contains:

```text
+----------------------+
| Header               |
+----------------------+
| Question             |
+----------------------+
| Answer               |
+----------------------+
| Authority             |
+----------------------+
| Additional            |
+----------------------+
```

### Header

Contains information such as:

* Query/response indicator
* Query ID
* Flags
* Counts

### Question

Contains what was requested.

Example:

```text
example.com
Type: A
```

### Answer

Contains the requested answer.

### Authority

Can provide authoritative/referral information.

### Additional

Can contain supplementary information.

---

# 21. DNS Caching

DNS responses can be cached.

Example:

```text
First request
Client → Resolver → DNS infrastructure
                  ↓
                 IP

Later request
Client → Resolver
           ↓
       Cached IP
```

Caching reduces:

* Latency
* DNS traffic
* Repeated lookups

---

# 22. TTL

TTL = **Time To Live**

In DNS, TTL controls how long a DNS response may be cached.

Example:

```text
A record
TTL = 300 seconds
```

The resolver can generally cache that answer for the specified TTL.

### Important

DNS TTL is **not** the same thing as IP packet TTL.

They are different concepts.

---

# 23. DNS Resolution vs HTTP Request

These are two different stages.

When visiting:

```text
https://example.com
```

A simplified flow is:

```text
1. DNS lookup
       ↓
example.com → IP

2. Network connection
       ↓
IP + port 443

3. TLS handshake
       ↓
HTTPS

4. HTTP request
       ↓
GET /

5. HTTP response
```

### Very Important Mental Model

```text
DNS answers:
"Where is the server?"

HTTP answers:
"What resource do I want?"

```

---

# 24. Practical DNS Commands

## Linux/Kali

### `dig`

Most useful DNS troubleshooting/recon command.

```bash
dig example.com
```

Specific record:

```bash
dig example.com A
dig example.com AAAA
dig example.com MX
dig example.com NS
dig example.com TXT
```

---

## `host`

Simple DNS lookup:

```bash
host example.com
```

Specific record:

```bash
host -t MX example.com
```

---

## `nslookup`

Available on both Windows and many Linux systems.

```bash
nslookup example.com
```

Windows:

```cmd
nslookup -type=MX example.com
```

---

# 25. Useful `dig` Options

### Short answer

```bash
dig +short example.com
```

### Reverse lookup

```bash
dig -x 8.8.8.8
```

### Query a specific DNS server

```bash
dig @8.8.8.8 example.com
```

Meaning:

```text
@8.8.8.8
   ↓
Ask this DNS server
```

Use only against systems/networks you are authorized to test.

---

# 26. DNS Recon Workflow

For an authorized lab/domain:

```text
                 Domain
                    |
                    ↓
              DNS Resolution
                    |
        +-----------+-----------+
        ↓           ↓           ↓
       A/AAAA       NS          MX
        ↓           ↓           ↓
       IPs      Name Servers   Mail
                    |
                    ↓
                  TXT
                    |
                    ↓
             DNS Information
```

Then combine the information with other authorized reconnaissance.

---

# 27. DNS Enumeration

DNS enumeration means collecting DNS-related information about a target.

Possible information:

```text
A
AAAA
MX
NS
TXT
CNAME
SOA
SRV
```

### Example

```bash
dig example.com A
dig example.com AAAA
dig example.com NS
dig example.com MX
dig example.com TXT
dig example.com SOA
```

---

# 28. Zone Transfer

A DNS zone transfer is a mechanism for copying DNS zone data between DNS servers.

Common types:

```text
AXFR
IXFR
```

### AXFR

Full zone transfer.

### IXFR

Incremental zone transfer.

---

# 29. Why Zone Transfers Matter in VAPT

If a DNS server incorrectly allows unauthorized zone transfers, an attacker may obtain a large amount of DNS information.

Conceptually:

```text
DNS Server
    |
    | Unauthorized zone transfer
    ↓
All/large portion of zone data
    |
    ↓
Potential hostnames + records
```

This can significantly increase the attack surface.

### Authorized Lab Testing

You may test a system you own or have explicit permission to assess.

Example:

```bash
dig @DNS_SERVER example.com AXFR
```

A secure public DNS server will generally refuse unauthorized transfers.

---

# 30. DNS Security Threats

DNS has several security concerns.

Important examples:

### 1. DNS Spoofing

An attacker causes false DNS information to be accepted.

Goal:

```text
victim
  ↓
fake DNS answer
  ↓
wrong destination
```

---

### 2. DNS Cache Poisoning

False DNS information is inserted into a resolver's cache.

Conceptually:

```text
Correct:
example.com → legitimate IP

Poisoned:
example.com → attacker-controlled IP
```

Modern DNS infrastructure has defenses designed to reduce this risk.

---

### 3. DNS Tunneling

DNS can sometimes be abused as a communication channel for unauthorized data transfer or command-and-control.

Conceptually:

```text
Compromised host
       |
       | DNS queries
       ↓
Attacker-controlled DNS infrastructure
```

Detection can involve analyzing:

* Query frequency
* Unusual subdomain lengths
* High-entropy labels
* Query patterns
* Unexpected DNS destinations

---

### 4. DNS Amplification

Attackers can abuse DNS servers in reflection/amplification attacks.

Conceptually:

```text
Spoofed victim IP
       ↓
DNS resolver/server
       ↓
Large response
       ↓
Victim
```

Properly configured DNS infrastructure and network controls help mitigate this.

---

# 31. DNSSEC

DNSSEC = **DNS Security Extensions**

DNSSEC provides mechanisms for validating the authenticity/integrity of DNS data.

It uses cryptographic signatures.

Conceptually:

```text
DNS record
    +
Digital signature
    ↓
Resolver validates
    ↓
Authentic / invalid
```

### Important

DNSSEC does **not** encrypt ordinary DNS queries.

It is primarily about **authentication/integrity of DNS data**, not DNS confidentiality.

---

# 32. DoH and DoT

Modern DNS can be transported using encrypted protocols.

## DNS over HTTPS — DoH

DNS queries are sent through HTTPS.

```text
DNS
 ↓
HTTPS
```

Typically:

```text
TCP 443
```

---

## DNS over TLS — DoT

DNS traffic is protected using TLS.

Typically:

```text
TCP 853
```

### Why Important?

Encryption can protect DNS queries from some forms of network observation, but it can also make enterprise monitoring and filtering more complicated.

---

# 33. DNS Security in Enterprise Networks

Organizations may use:

```text
Client
  ↓
Internal DNS Resolver
  ↓
Security Filtering
  ↓
Internet DNS
```

Security controls can detect/block:

* Malicious domains
* Phishing infrastructure
* Malware domains
* Known C2 domains
* Newly registered suspicious domains

DNS can therefore be an important security telemetry source.

---

# 34. DNS in VAPT Attack-Surface Mapping

Imagine:

```text
example.com
│
├── www.example.com
├── api.example.com
├── vpn.example.com
├── mail.example.com
└── dev.example.com
```

A pentester may investigate which hosts are:

```text
Internet-facing
Internal-only
Cloud-hosted
Third-party hosted
Development environments
```

This helps build an **attack-surface map**.

---

# 35. DNS + Subdomain Enumeration

DNS information can assist with discovering subdomains.

For example:

```text
example.com
      ↓
Possible subdomains
      ↓
api.example.com
dev.example.com
mail.example.com
vpn.example.com
```

### Important distinction

DNS enumeration is **not the same as vulnerability discovery**.

Finding:

```text
dev.example.com
```

doesn't mean it is vulnerable.

It means:

> "This asset exists and should be investigated within scope."

---

# 36. DNS Misconfigurations

Common security issues include:

### Unauthorized zone transfer

```text
AXFR allowed to unauthorized clients
```

### Internal hostname exposure

Public DNS may accidentally reveal infrastructure naming.

### Dangling DNS records

A DNS record may point to a resource that no longer exists.

Potentially relevant to:

```text
Subdomain takeover
```

But verification is required.

### Weak DNS administration

Poor access control can expose or alter DNS infrastructure.

---

# 37. DNS Troubleshooting Workflow

If:

```text
example.com
```

doesn't work:

### Step 1

Check resolution:

```bash
dig example.com
```

### Step 2

Check A/AAAA:

```bash
dig example.com A
dig example.com AAAA
```

### Step 3

Check authoritative servers:

```bash
dig example.com NS
```

### Step 4

Ask a specific resolver:

```bash
dig @8.8.8.8 example.com
```

### Step 5

Check the network path and application separately.

Remember:

```text
DNS failure ≠ HTTP failure
```

---

# 38. Real-World Example

Suppose:

```text
https://shop.example.com
```

is not loading.

You investigate.

### DNS

```text
shop.example.com
        ↓
203.0.113.20
```

DNS works.

Then:

```text
203.0.113.20:443
```

is reachable.

Then HTTPS/TLS is established.

Then:

```text
GET /
```

returns:

```text
HTTP/1.1 200 OK
```

Therefore:

```text
DNS     → Working
Network → Working
TLS     → Working
HTTP    → Working
```

This layered thinking is extremely important for troubleshooting and VAPT.

---

# 39. Python DNS Automation

Since you're learning Python for VAPT, DNS can be automated.

Install:

```bash
pip install dnspython
```

Basic example:

```python
import dns.resolver

target = "example.com"

for record_type in ["A", "AAAA", "MX", "NS"]:
    try:
        answers = dns.resolver.resolve(target, record_type)

        print(f"\n[{record_type}]")

        for answer in answers:
            print(answer)

    except Exception as e:
        print(f"{record_type}: unavailable")
```

### What are you learning?

Not just DNS.

You're practicing:

```text
Python
  ↓
Library
  ↓
DNS query
  ↓
Response parsing
  ↓
Automation
```

---

# 40. DNS + Nmap

Nmap can also help with authorized host/service discovery.

For example:

```bash
nmap -sV scanme.nmap.org
```

The important workflow is:

```text
DNS
 ↓
Hostname/IP
 ↓
Port discovery
 ↓
Service detection
 ↓
Application testing
```

DNS is therefore often near the beginning of a security assessment.

---

# 41. Common Beginner Mistakes

### ❌ Mistake 1

Thinking DNS only converts domains to IPv4.

Correct:

```text
DNS can provide many types of information.
```

---

### ❌ Mistake 2

Thinking DNS always uses UDP.

Correct:

```text
DNS uses both UDP and TCP depending on circumstances.
```

---

### ❌ Mistake 3

Confusing A and AAAA.

Remember:

```text
A    → IPv4
AAAA → IPv6
```

---

### ❌ Mistake 4

Confusing MX with NS.

```text
MX → Mail servers
NS → Name servers
```

---

### ❌ Mistake 5

Thinking CNAME directly stores an IP.

Usually:

```text
CNAME → hostname
```

The target hostname can then resolve to an IP.

---

### ❌ Mistake 6

Thinking DNSSEC encrypts DNS.

It doesn't provide general DNS query confidentiality.

---

### ❌ Mistake 7

Assuming a discovered subdomain is automatically vulnerable.

Discovery:

```text
Asset found
```

Vulnerability:

```text
Security weakness verified
```

These are different.

---

# 42. Must-Know DNS Table

| Concept              | Meaning                           |
| -------------------- | --------------------------------- |
| DNS                  | Domain Name System                |
| Resolver             | Finds DNS answers for clients     |
| Root                 | Top of DNS hierarchy              |
| TLD                  | `.com`, `.org`, `.net`, etc.      |
| Authoritative server | Holds authoritative zone data     |
| A                    | IPv4 address                      |
| AAAA                 | IPv6 address                      |
| CNAME                | Hostname alias                    |
| MX                   | Mail server                       |
| NS                   | Name server                       |
| TXT                  | Text/policy information           |
| PTR                  | Reverse DNS                       |
| SOA                  | Zone authority information        |
| SRV                  | Service location                  |
| TTL                  | DNS cache lifetime                |
| AXFR                 | Full zone transfer                |
| IXFR                 | Incremental zone transfer         |
| DNSSEC               | DNS data authentication/integrity |
| DoH                  | DNS over HTTPS                    |
| DoT                  | DNS over TLS                      |

---

# 43. VAPT Mental Model

When you see a domain:

```text
example.com
```

Think:

```text
                DOMAIN
                   │
                   ▼
             DNS Resolution
                   │
       ┌───────────┼───────────┐
       ▼           ▼           ▼
      A/AAAA       NS          MX
       │           │           │
       ▼           ▼           ▼
      IPs       DNS infra     Mail
       │
       ▼
   Subdomains
       │
       ▼
   Attack Surface
       │
       ▼
 Port / Service Discovery
       │
       ▼
 Application Testing
```

This is the important connection between **networking knowledge and VAPT**.

---

# 44. Interview Questions

### Q1. What is DNS?

DNS translates domain names into information such as IP addresses and other DNS records.

### Q2. What is an A record?

Maps a hostname to an IPv4 address.

### Q3. What is an AAAA record?

Maps a hostname to an IPv6 address.

### Q4. What is an MX record?

Specifies mail servers for a domain.

### Q5. What is an NS record?

Identifies authoritative name servers for a DNS zone.

### Q6. What is a CNAME?

An alias from one hostname to another hostname.

### Q7. What is a recursive resolver?

A resolver that performs DNS resolution on behalf of the client.

### Q8. What is DNS caching?

Temporarily storing DNS responses to reduce repeated lookups.

### Q9. What is TTL?

The amount of time a DNS response can generally remain cached.

### Q10. What is DNSSEC?

A set of DNS extensions that provides cryptographic validation of DNS data.

### Q11. Does DNS use TCP or UDP?

Both.

### Q12. What is reverse DNS?

Resolving an IP address to a hostname, commonly using PTR records.

### Q13. What is AXFR?

A full DNS zone transfer.

### Q14. Why is unauthorized zone transfer a security concern?

It can expose large amounts of DNS information and increase the attacker's knowledge of the environment.

### Q15. Why is DNS important during VAPT?

It can reveal infrastructure, hosts, services, and potential attack-surface information.

---

# 45. Scenario-Based Questions

### Scenario 1

You find:

```text
mail.example.com
MX → mail.provider.com
```

What does this tell you?

**Answer:** The domain's mail handling involves the identified mail infrastructure.

---

### Scenario 2

You discover:

```text
dev.example.com
```

Is it automatically vulnerable?

**Answer:** No. It is an identified asset that requires authorized investigation.

---

### Scenario 3

A DNS server allows AXFR to unauthorized clients.

What could this expose?

**Answer:** Zone information such as hostnames and DNS records.

---

### Scenario 4

A domain has:

```text
A
AAAA
```

What does this suggest?

**Answer:** The domain has both IPv4 and IPv6 DNS addressing information.

---

# 46. MCQ Practice

### Q1. DNS stands for:

A. Domain Network System
B. Domain Name System
C. Data Name Service
D. Domain Node Service

**Answer: B**

---

### Q2. Which record maps a hostname to IPv4?

A. MX
B. AAAA
C. A
D. PTR

**Answer: C**

---

### Q3. Which record is associated with IPv6?

A. A
B. AAAA
C. MX
D. NS

**Answer: B**

---

### Q4. Which record specifies mail servers?

A. MX
B. NS
C. PTR
D. SOA

**Answer: A**

---

### Q5. Which record is commonly used for reverse DNS?

A. A
B. CNAME
C. PTR
D. MX

**Answer: C**

---

### Q6. DNS commonly uses:

A. Only TCP
B. Only UDP
C. TCP and UDP
D. HTTP only

**Answer: C**

---

### Q7. What does `dig` primarily do?

A. Packet capture
B. DNS queries
C. Password cracking
D. File encryption

**Answer: B**

---

### Q8. What does AXFR mean?

A. Automatic Forward Exchange
B. Authoritative Forward Exchange
C. Full zone transfer
D. DNS encryption

**Answer: C**

---

### Q9. DNSSEC primarily provides:

A. DNS compression
B. Cryptographic validation of DNS data
C. Faster HTTP
D. IP address allocation

**Answer: B**

---

### Q10. Which is a public DNS resolver?

A. 8.8.8.8
B. 192.168.1.1 always
C. 127.0.0.1 always
D. 0.0.0.0

**Answer: A**

---

# 47. Practical Lab Ideas

## 🟢 Lab 1 — DNS Basics

Use a permitted test domain.

Practice:

```bash
dig example.com
dig example.com A
dig example.com AAAA
dig example.com MX
dig example.com NS
dig example.com TXT
```

Goal:

> Learn to read DNS responses.

---

## 🟡 Lab 2 — Build a DNS Recon Script

Use Python + `dnspython`.

Your script should:

```text
Input domain
     ↓
A
AAAA
MX
NS
TXT
     ↓
Save results
```

Goal:

> Learn DNS automation.

---

## 🔴 Lab 3 — DNS Security Lab

Build/use a local lab DNS environment and study:

```text
DNS query
DNS response
Caching
TTL
Authoritative server
Zone transfer concepts
DNSSEC
```

Goal:

> Understand how DNS security failures happen without testing unauthorized infrastructure.

---

# 48. What You Should Actually Memorize

Don't memorize every DNS packet field.

Prioritize:

```text
DNS
Resolver
Authoritative server
Root
TLD
A
AAAA
CNAME
MX
NS
TXT
PTR
SOA
SRV
UDP/TCP 53
TTL
Recursive vs iterative
DNSSEC
DoH
DoT
AXFR
DNS tunneling concept
DNS poisoning concept
```

---

# 49. Final Cheat Sheet

```text
DNS = Domain Name System

A       → IPv4
AAAA    → IPv6
CNAME   → Alias
MX      → Mail
NS      → Name server
TXT     → Text/policies
PTR     → Reverse DNS
SOA     → Zone authority
SRV     → Service location

UDP 53  → Common DNS transport
TCP 53  → Also used when required

dig     → DNS queries
host    → Simple DNS lookup
nslookup → DNS lookup

AXFR    → Full zone transfer
IXFR    → Incremental zone transfer

DNSSEC  → DNS data authenticity/integrity
DoH     → DNS over HTTPS
DoT     → DNS over TLS
```

---

# 50. Final Mental Model

The easiest way to understand DNS is:

```text
                 USER
                   │
                   │ example.com
                   ▼
              DNS RESOLVER
                   │
          ┌────────┴────────┐
          ▼                 ▼
       CACHE            DNS HIERARCHY
                           │
                    ┌──────┴──────┐
                    ▼             ▼
                  ROOT           TLD
                                  │
                                  ▼
                         AUTHORITATIVE DNS
                                  │
                                  ▼
                            DNS RECORD
                                  │
                         ┌────────┴────────┐
                         ▼                 ▼
                       IPv4              IPv6
                         │                 │
                         └────────┬────────┘
                                  ▼
                             SERVER IP
                                  │
                                  ▼
                            TCP/UDP Service
                                  │
                                  ▼
                         HTTP / SSH / etc.
```

### The key VAPT mindset:

> **DNS is not just "domain → IP". DNS is an information source that helps you understand an organization's infrastructure and attack surface.**

Always distinguish **discovery** from **vulnerability** and perform enumeration/testing only against systems you're authorized to assess.

---
