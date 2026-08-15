

# 1. What You'll Learn

By the end of this lesson, you should understand:

* DNS spoofing
* DNS cache poisoning
* DNS hijacking
* DNS amplification
* DNS rebinding
* Zone-transfer misconfiguration
* DNS tunneling
* DNS tunneling detection
* DNS exfiltration concept
* DNS-based C2 concept
* DNSSEC
* DoH/DoT security considerations
* VAPT testing methodology
* Blue-team detection techniques

> ⚠️ **Lab/Safety:** DNS tunneling and C2 techniques can be abused to hide unauthorized communication. The practical examples below focus on **local/authorized labs, detection, and analysis**, rather than providing instructions for covert data theft or evasion.

---

# 2. Why DNS Is a Security Target

DNS is extremely important because almost every Internet-connected system uses it.

Normal flow:

```text
Application
    ↓
DNS Query
    ↓
DNS Resolver
    ↓
DNS Response
    ↓
Application connects to destination
```

If DNS is manipulated:

```text
User
 ↓
DNS
 ↓
Wrong / malicious answer
 ↓
Wrong destination
```

Therefore DNS attacks can affect:

* Availability
* Integrity
* Confidentiality
* Authentication
* Traffic routing

---

# 3. DNS Security Attack Surface

Think of DNS as multiple components:

```text
                    DNS Infrastructure
                           │
        ┌──────────────────┼──────────────────┐
        ↓                  ↓                  ↓
     Client             Resolver         Authoritative
        │                  │                  │
        ↓                  ↓                  ↓
    Queries             Cache             Records
                           │
                           ↓
                       Internet
```

Each component has different security risks.

---

# 4. DNS Attack Categories

| Attack                 | Main Goal                               |
| ---------------------- | --------------------------------------- |
| DNS Spoofing           | Provide false DNS information           |
| Cache Poisoning        | Insert malicious information into cache |
| DNS Hijacking          | Redirect DNS resolution                 |
| DNS Amplification      | DDoS/reflection                         |
| DNS Rebinding          | Abuse DNS changes against applications  |
| Zone Transfer Exposure | Obtain DNS zone information             |
| DNS Tunneling          | Abuse DNS as a communication channel    |
| DNS-based C2           | Command/control over DNS                |
| DNS Exfiltration       | Move data through DNS                   |
| Dangling DNS           | Potential subdomain takeover            |

---

# 5. DNS Spoofing

## Definition

DNS spoofing occurs when an attacker causes a victim to receive a false DNS response.

Normal:

```text
example.com
     ↓
Legitimate IP
```

Spoofed:

```text
example.com
     ↓
Incorrect / attacker-controlled IP
```

---

# 6. Why DNS Spoofing Is Dangerous

Suppose:

```text
bank.example
     ↓
Legitimate Server
```

becomes:

```text
bank.example
     ↓
Malicious Server
```

The victim may unknowingly communicate with the wrong destination.

Potential impact:

* Phishing
* Credential theft
* Traffic redirection
* Malware delivery
* Loss of trust

---

# 7. DNS Cache Poisoning

DNS caching improves performance.

But if false DNS data is inserted into a resolver's cache, users can receive incorrect answers.

Normal:

```text
Resolver Cache

example.com → Legitimate IP
```

Poisoned:

```text
Resolver Cache

example.com → Malicious IP
```

---

# 8. How Cache Poisoning Works — Conceptually

```text
Victim
  │
  │ DNS query
  ▼
Resolver
  │
  │ Incorrect DNS data
  ▼
Cache
  │
  ▼
Victim receives wrong IP
```

Modern resolvers use protections such as:

* Randomized query identifiers
* Source-port randomization
* DNSSEC validation
* Proper resolver configuration

---

# 9. DNS Hijacking

DNS hijacking means DNS resolution is redirected or manipulated.

Possible targets:

* Client configuration
* Router
* DNS resolver
* Authoritative DNS infrastructure
* Domain account

Example:

```text
User
 ↓
Compromised DNS configuration
 ↓
Attacker-controlled resolver
 ↓
Manipulated response
```

---

# 10. DNS Hijacking vs DNS Spoofing

Don't confuse them.

### Spoofing

Focus:

> False DNS response/information.

### Hijacking

Focus:

> Redirecting or taking control of DNS resolution/infrastructure.

They can overlap conceptually, but they're not identical terms.

---

# 11. DNS Amplification

DNS can be abused in reflection/amplification attacks.

Basic concept:

```text
Attacker
   │
   │ Spoofed source IP
   ▼
DNS Server
   │
   │ Larger response
   ▼
Victim
```

The victim receives traffic it didn't request.

---

# 12. Why Amplification Works

Some DNS requests can produce responses significantly larger than the request.

Conceptually:

```text
Small request
     ↓
Large response
     ↓
Victim
```

Attackers can abuse many third-party servers to generate traffic toward a victim.

---

# 13. Defensive Measures Against DNS Amplification

Organizations can reduce risk through:

* Preventing IP spoofing where possible
* Proper resolver configuration
* Restricting recursive resolution
* Rate limiting
* Network filtering
* DDoS protection
* Monitoring unusual DNS traffic

---

# 14. DNS Rebinding

DNS rebinding is an attack technique involving changing DNS resolution over time.

Conceptually:

```text
First DNS response
example.com → Public IP

Later DNS response
example.com → Internal/private IP
```

This can potentially bypass assumptions made by browser applications about where a hostname points.

---

# 15. Why DNS Rebinding Matters

Modern applications sometimes expose services on:

```text
localhost
127.0.0.1
192.168.x.x
10.x.x.x
```

DNS rebinding can become relevant when an application incorrectly trusts requests based only on hostname/origin assumptions.

### Security lesson

> Never assume a hostname automatically makes an application trustworthy.

---

# 16. Zone Transfer Misconfiguration

DNS zone transfers synchronize DNS information between DNS servers.

Two common types:

```text
AXFR → Full zone transfer
IXFR → Incremental zone transfer
```

If improperly configured, unauthorized clients may obtain zone information.

Example information could include:

```text
dev.example.com
vpn.example.com
mail.example.com
internal.example.com
```

---

# 17. Why Zone Transfers Matter in VAPT

A successful unauthorized transfer can expose:

* Hostnames
* IP addresses
* DNS records
* Internal naming patterns
* Infrastructure structure

This can increase reconnaissance efficiency.

### Important

Zone transfer exposure is an **information-disclosure issue**. It does not automatically mean the exposed hosts are vulnerable.

---

# 18. Testing Zone Transfer Safely

Only test domains/servers that are explicitly within your authorized scope.

Conceptually, the assessment is:

```text
Authorized DNS Server
       ↓
Request AXFR
       ↓
Allowed?
   ↙       ↘
Yes        No
 ↓          ↓
Review     Expected
zone       rejection
```

The goal is to determine whether access control is correctly implemented.

---

# 19. What Is DNS Tunneling?

**DNS tunneling** is the abuse of DNS queries/responses to transport information that is not normally part of ordinary DNS resolution.

Normal:

```text
Client
 ↓
"What is the IP of example.com?"
 ↓
DNS response
```

Tunneling:

```text
Client
 ↓
Encoded information inside DNS-related traffic
 ↓
DNS infrastructure
 ↓
Destination controlled by an attacker/operator
```

---

# 20. Why DNS Can Be Used for Tunneling

DNS is attractive to attackers because:

* DNS is widely permitted through firewalls
* Organizations depend on DNS
* DNS traffic is common
* DNS works across many networks
* DNS can carry text-like labels

This makes DNS an interesting covert-channel candidate.

---

# 21. Basic DNS Tunneling Concept

Imagine data:

```text
HELLO
```

is transformed into an encoded representation.

Conceptually:

```text
Data
 ↓
Encoding
 ↓
DNS-safe label
 ↓
subdomain.example.test
 ↓
DNS query
```

The receiving infrastructure observes the queries and reconstructs the information.

### Important

The exact encoding mechanism can vary.

Common encodings can include:

* Hexadecimal
* Base32
* Other DNS-compatible representations

---

# 22. DNS Query Structure

A DNS name consists of labels.

Example:

```text
abc123.example.com
```

Breakdown:

```text
abc123
   ↓
Label

example
   ↓
Domain label

com
   ↓
TLD
```

Tunneling can abuse the label portion to transport information.

---

# 23. Normal vs Suspicious DNS

### Normal

```text
www.example.com
api.example.com
mail.example.com
cdn.example.com
```

Usually:

* Shorter labels
* Predictable names
* Repeated normal patterns

### Potentially suspicious

```text
aj3k29f8s7d2.example.test
8fj39dk2ls9x.example.test
p9x82kd73m1q.example.test
```

Potential indicators:

* Long labels
* High randomness
* High entropy
* Many unique subdomains
* Very high query volume

**One suspicious-looking query is not proof of tunneling.**

---

# 24. DNS Tunneling Detection

A defender can look for:

### 1. Query frequency

```text
Normal:
Few queries

Suspicious:
Hundreds/thousands of unique queries
```

---

### 2. Long subdomains

Example:

```text
normal.example.com
```

vs.

```text
a8fj29skd73j2ks8d9.example.com
```

Long labels deserve investigation.

---

### 3. High entropy

Random-looking strings can indicate encoded data.

Conceptually:

```text
Normal:
server01

Potentially suspicious:
a8F3kL9xQ2zP7m
```

Entropy is a **signal**, not proof.

---

### 4. High NXDOMAIN rate

NXDOMAIN means:

> The queried domain/name does not exist.

Tunneling infrastructure may generate many dynamically created names, potentially causing unusual NXDOMAIN patterns.

Again:

> High NXDOMAIN volume alone does not prove tunneling.

---

# 25. DNS Tunneling Detection Pipeline

A useful defensive workflow:

```text
DNS Logs
   ↓
Collect queries
   ↓
Normalize
   ↓
Calculate features
   ├── Length
   ├── Entropy
   ├── Frequency
   ├── NXDOMAIN ratio
   ├── Unique subdomains
   └── Domain reputation
   ↓
Correlation
   ↓
Alert / Investigation
```

This is a great cybersecurity automation project.

---

# 26. DNS Exfiltration

**Exfiltration** means unauthorized transfer of data out of a system.

DNS can potentially be abused as an exfiltration channel.

Conceptually:

```text
Sensitive Data
      ↓
Encoding
      ↓
DNS queries
      ↓
DNS infrastructure
      ↓
External system
```

Possible target data could include:

* Host information
* Identifiers
* Configuration fragments
* Other sensitive data

For safe learning, use **dummy data in a local lab**.

---

# 27. DNS-Based Command & Control

DNS can also serve as a communication channel between compromised systems and external infrastructure.

Conceptually:

```text
Compromised Host
       │
       │ DNS query
       ▼
C2 Infrastructure
       │
       │ DNS response
       ▼
Compromised Host
```

This is known as **DNS-based C2**.

---

# 28. DNS C2 vs Normal DNS

Normal:

```text
Client
 ↓
DNS query
 ↓
Resolver
 ↓
IP response
```

Potential C2:

```text
Compromised host
 ↓
Repeated unusual DNS queries
 ↓
Attacker-controlled domain
 ↓
Instructions/data
```

The traffic may look superficially like ordinary DNS, which is why behavioral analysis matters.

---

# 29. DNS Tunneling vs DNS C2

They're related but not identical.

### DNS tunneling

General technique of transporting data through DNS.

### DNS C2

Using DNS as a communication mechanism for command-and-control.

A C2 channel may use tunneling techniques, but the terms shouldn't automatically be treated as synonyms.

---

# 30. DNSSEC and Tunneling

DNSSEC helps validate DNS data authenticity/integrity.

However:

> DNSSEC does not automatically prevent DNS tunneling.

Why?

Because tunneling can use legitimate-looking DNS infrastructure while abusing the **content/pattern of queries**.

Therefore:

```text
DNSSEC
   ↓
Helps authenticate DNS data

Traffic analysis
   ↓
Helps detect suspicious DNS behavior
```

These solve different problems.

---

# 31. DoH and DoT Security Considerations

Encrypted DNS can improve privacy.

Examples:

```text
DoH → DNS over HTTPS
DoT → DNS over TLS
```

But from an enterprise security perspective, encrypted DNS can make traditional DNS inspection more difficult if organizations don't have appropriate visibility and policy controls.

Therefore security teams may need:

* Endpoint telemetry
* Resolver logs
* Network metadata
* Approved DNS infrastructure
* Policy enforcement

---

# 32. Dangling DNS Records

Consider:

```text
blog.example.com
      ↓
CNAME
      ↓
old-service.provider.com
```

If the external resource is deleted but the DNS record remains, the record may become **dangling**.

In some configurations, this can contribute to a **subdomain takeover** vulnerability.

---

# 33. Important Distinction

This:

```text
CNAME → deleted resource
```

does **not automatically mean**:

```text
Subdomain takeover confirmed
```

A tester must verify whether:

1. The external resource is actually unclaimed.
2. The provider permits another party to claim it.
3. The DNS configuration points to it.
4. The claim can actually be performed.
5. The result is within authorized scope.

---

# 34. VAPT DNS Assessment Workflow

A professional assessment can follow:

```text
                 DNS Assessment
                       │
                       ▼
                 Identify scope
                       │
                       ▼
                DNS enumeration
                       │
             ┌─────────┼─────────┐
             ▼         ▼         ▼
            A/AAAA    NS/MX     TXT
             │
             ▼
          CNAME/SRV
             │
             ▼
      Check configuration
             │
       ┌─────┼─────┐
       ▼     ▼     ▼
      AXFR  DNSSEC  Exposure
       │
       ▼
   Security testing
       │
       ▼
      Validate
       │
       ▼
      Report
```

---

# 35. VAPT Finding Examples

## Finding 1 — Unauthorized Zone Transfer

**Title:**

> DNS Zone Transfer Permitted to Unauthorized Client

**Impact:**

Potential disclosure of DNS zone information.

**Evidence:**

Authorized lab/test environment demonstrates that AXFR is accepted by an unauthorized client.

**Recommendation:**

Restrict zone transfers to authorized secondary DNS servers.

---

# 36. Finding 2 — Dangling DNS Record

**Title:**

> Potential Dangling DNS Record

**Impact:**

May create a potential subdomain takeover condition depending on the third-party service.

**Recommendation:**

Remove unused DNS records or correctly reclaim/configure the referenced service.

---

# 37. Finding 3 — Suspicious DNS Behavior

A security monitoring system may detect:

```text
Large number of unique subdomains
+
Long labels
+
High entropy
+
Frequent requests
```

This could be:

* DNS tunneling
* Malware
* CDN behavior
* Tracking
* Software telemetry
* Legitimate dynamic systems

Therefore the finding requires **correlation and validation**.

---

# 38. Safe DNS Tunneling Lab

You can learn the concept without touching real organizations.

## Lab Architecture

```text
Windows/Kali
      │
      ▼
Local Test Machine
      │
      ▼
Local DNS Service
      │
      ▼
Wireshark / DNS Logs
```

Use dummy data such as:

```text
TEST123
HELLOLAB
TRAININGDATA
```

Observe:

```text
DNS Query
   ↓
Domain
   ↓
Subdomain labels
   ↓
Encoded-looking data
   ↓
DNS logs
```

### Learning objective

Understand:

```text
How the traffic looks
How it differs from normal DNS
How defenders detect it
```

---

# 39. Wireshark Practice

In an authorized lab, capture DNS traffic.

Useful display filter:

```text
dns
```

You can inspect:

* Query name
* Query type
* Response
* Response code
* TTL
* Source
* Destination

For suspicious DNS analysis, look for:

```text
dns.qry.name
```

and compare:

```text
Length
Frequency
Uniqueness
Patterns
```

---

# 40. Useful Linux Commands for DNS Analysis

### Basic lookup

```bash
dig example.com
```

### Record type

```bash
dig example.com TXT
```

### Reverse lookup

```bash
dig -x 8.8.8.8
```

### Short output

```bash
dig +short example.com
```

### Specific resolver

```bash
dig @8.8.8.8 example.com
```

Use these against systems/domains you are authorized to test.

---

# 41. Python Defensive DNS Analysis

You can build a simple detector that analyzes DNS logs.

Example concept:

```python
from collections import Counter

queries = [
    "www.example.com",
    "api.example.com",
    "mail.example.com",
    "a8fj29skd73.example.com",
]

for query in queries:
    label = query.split(".")[0]

    if len(label) > 20:
        print("[!] Long DNS label:", query)
```

This is intentionally simple.

The real skill is extending it with:

```text
Length
Entropy
Frequency
NXDOMAIN rate
Unique subdomain count
Allowlists
Reputation
Time windows
```

---

# 42. Advanced Detection Features

For a serious DNS analytics project, calculate:

### Feature 1 — Query length

```text
Longer query
      ↓
Higher suspicion
```

Not necessarily malicious.

---

### Feature 2 — Shannon entropy

Random/encoded strings may have higher entropy.

Conceptually:

```text
Low entropy
→ predictable characters

High entropy
→ more random distribution
```

---

### Feature 3 — Unique subdomain ratio

Example:

```text
1000 queries
900 unique labels
```

could be worth investigation.

---

### Feature 4 — Query rate

Measure:

```text
queries / minute
```

---

### Feature 5 — NXDOMAIN percentage

Track:

```text
NXDOMAIN responses
------------------
Total queries
```

---

# 43. Advanced Detection Architecture

```text
                 DNS Logs
                    │
                    ▼
              Data Collector
                    │
                    ▼
             Normalization
                    │
        ┌───────────┼───────────┐
        ▼           ▼           ▼
     Length      Entropy      Frequency
        │           │           │
        └───────────┼───────────┘
                    ▼
              Risk Scoring
                    │
                    ▼
               Correlation
                    │
          ┌─────────┴─────────┐
          ▼                   ▼
        Alert              Benign
          │
          ▼
      Investigation
```

This is a strong **blue-team + Python project**.

---

# 44. Common Beginner Mistakes

### ❌ 1. "Every long DNS query is tunneling."

Wrong.

Long labels can be legitimate.

---

### ❌ 2. "High entropy means malware."

Wrong.

Entropy is only one indicator.

---

### ❌ 3. "DNSSEC stops DNS tunneling."

Wrong.

DNSSEC and tunneling solve different security problems.

---

### ❌ 4. "DNS always uses UDP."

Wrong.

DNS can use both UDP and TCP.

---

### ❌ 5. "CNAME means subdomain takeover."

Wrong.

A dangling CNAME requires additional validation.

---

### ❌ 6. "DoH is automatically more secure for every organization."

Oversimplified.

DoH provides encryption/privacy benefits but can complicate enterprise DNS visibility and policy enforcement.

---

# 45. Interview Questions

### Q1. What is DNS tunneling?

Using DNS traffic as a communication channel for data that is not part of ordinary DNS resolution.

### Q2. Why is DNS attractive for tunneling?

Because DNS is ubiquitous and commonly permitted across networks.

### Q3. What is DNS exfiltration?

Unauthorized transfer of data from a system using DNS as a channel.

### Q4. What is DNS-based C2?

Using DNS communication as a command-and-control channel.

### Q5. What is DNS cache poisoning?

Inserting false DNS information into a resolver's cache.

### Q6. What is DNS amplification?

Abusing DNS infrastructure to generate amplified traffic toward a victim.

### Q7. What is AXFR?

Full DNS zone transfer.

### Q8. What can unauthorized AXFR expose?

Potentially a large amount of DNS zone information.

### Q9. Does DNSSEC prevent DNS tunneling?

No.

### Q10. What indicators may suggest DNS tunneling?

Potential indicators include unusually long/high-entropy labels, high query volume, many unique subdomains, and unusual NXDOMAIN patterns.

---

# 46. Scenario Questions

### Scenario 1

A workstation sends thousands of DNS queries per minute to one unusual domain.

What do you investigate?

**Answer:**

* Query frequency
* Subdomain uniqueness
* Label length
* Entropy
* NXDOMAIN rate
* Destination/domain reputation
* Process generating the requests

---

### Scenario 2

You find an AXFR response containing hundreds of hostnames.

What's the security concern?

**Answer:**

Potential DNS information disclosure caused by improper zone-transfer access control.

---

### Scenario 3

A DNS label looks random.

Is it automatically tunneling?

**Answer:**

No. It is only an indicator requiring further analysis.

---

### Scenario 4

A CNAME points to an unavailable third-party resource.

Is takeover confirmed?

**Answer:**

No. The takeover condition must be validated safely and within scope.

---

# 47. MCQ Practice

### Q1. DNS tunneling primarily abuses:

A. DNS as a communication channel
B. TCP handshake
C. HTTP cookies
D. ARP tables

**Answer: A**

---

### Q2. AXFR represents:

A. DNS encryption
B. Full zone transfer
C. DNS caching
D. Reverse DNS

**Answer: B**

---

### Q3. Which can be an indicator of DNS tunneling?

A. High query frequency
B. Long random-looking labels
C. Many unique subdomains
D. All of the above

**Answer: D**

---

### Q4. DNSSEC primarily helps with:

A. DNS data authenticity/integrity
B. Increasing bandwidth
C. Password hashing
D. HTTP encryption

**Answer: A**

---

### Q5. DNS amplification is associated with:

A. DDoS/reflection
B. SQL injection
C. Password hashing
D. File permissions

**Answer: A**

---

### Q6. DNS rebinding can involve:

A. Changing DNS resolution over time
B. Changing a file extension
C. Replacing an HTTP header
D. Changing an SSH key

**Answer: A**

---

### Q7. Which is NOT proof of DNS tunneling?

A. One long DNS label
B. High query rate
C. High entropy
D. Multiple correlated indicators

**Answer: A**

---

### Q8. DNS exfiltration means:

A. DNS caching
B. Unauthorized data transfer using DNS
C. DNS record deletion
D. DNS compression

**Answer: B**

---

### Q9. DoH means:

A. DNS over HTTP
B. DNS over HTTPS
C. Data over HTTPS
D. DNS over Host

**Answer: B**

---

### Q10. DoT normally uses:

A. TCP 21
B. UDP 53
C. TCP 853
D. TCP 25

**Answer: C**

---

### Q11. Which attack involves false DNS information being inserted into a cache?

A. Cache poisoning
B. Port scanning
C. SQL injection
D. CSRF

**Answer: A**

---

### Q12. A dangling CNAME can potentially lead to:

A. Subdomain takeover
B. TCP fragmentation
C. ARP poisoning
D. Password spraying

**Answer: A**

---

### Q13. Which is the best response to "high DNS entropy means malware"?

A. Always true
B. Always false
C. It is an indicator requiring correlation
D. It proves tunneling

**Answer: C**

---

### Q14. DNS-based C2 means:

A. DNS used for command/control communication
B. DNS used only for email
C. DNS used for file compression
D. DNS used to create TCP packets

**Answer: A**

---

### Q15. Which is a good defense against unauthorized zone transfers?

A. Allow AXFR from everyone
B. Restrict transfers to authorized servers
C. Disable HTTP
D. Change SSH port

**Answer: B**

---

# 48. Practical Projects

## 🟢 Project 1 — DNS Recon Tool

Build:

```text
dns_recon.py
```

Input:

```text
example.com
```

Output:

```text
A:
AAAA:
MX:
NS:
TXT:
CNAME:
SOA:
```

Skills:

* Python
* `dnspython`
* Error handling
* File output

---

## 🟡 Project 2 — DNS Anomaly Detector

Input:

```text
dns.log
```

Calculate:

```text
Query count
Unique domains
Longest labels
NXDOMAIN count
Average label length
```

Output:

```text
Potentially suspicious domains
```

Skills:

* Python
* Regex/string processing
* Statistics
* Log analysis

---

## 🔴 Project 3 — DNS Security Dashboard

Build a small Python application:

```text
DNS Logs
   ↓
Parser
   ↓
Feature Extraction
   ↓
Risk Scoring
   ↓
Dashboard
```

Display:

```text
Top queried domains
Top NXDOMAIN domains
Longest labels
High-entropy labels
Query frequency
Potential anomalies
```

This is an excellent portfolio project because it combines:

```text
Networking
+
DNS
+
Python
+
Cybersecurity
+
Detection
+
Data analysis
```

---

# 49. Must-Memorize Cheat Sheet

```text
DNS SPOOFING
→ False DNS information

CACHE POISONING
→ False information inserted into resolver cache

DNS HIJACKING
→ DNS resolution/infrastructure redirected or controlled

DNS AMPLIFICATION
→ Reflection/amplification → DDoS

DNS REBINDING
→ DNS answer changes to manipulate where a hostname resolves

AXFR
→ Full zone transfer

IXFR
→ Incremental zone transfer

DNS TUNNELING
→ DNS abused as a data communication channel

DNS EXFILTRATION
→ Data moved out using DNS

DNS C2
→ Command/control using DNS

DNSSEC
→ Authenticity/integrity validation

DoH
→ DNS over HTTPS

DoT
→ DNS over TLS
```

---

# 50. Final Mental Model

Don't memorize DNS attacks as random names.

Think:

```text
                       DNS
                        │
        ┌───────────────┼────────────────┐
        │               │                │
        ▼               ▼                ▼
     Integrity       Availability      Confidentiality
        │               │                │
        ▼               ▼                ▼
    Spoofing        Amplification      Tunneling
    Poisoning                         Exfiltration
    Hijacking                           C2
        │
        └───────────────┐
                        ▼
                    Rebinding
```

And for **DNS tunneling detection**:

```text
                 DNS Traffic
                     │
                     ▼
              Is behavior unusual?
                     │
        ┌────────────┴────────────┐
        ▼                         ▼
       No                         Yes
        │                          │
     Normal              Investigate signals
                                   │
                     ┌─────────────┼─────────────┐
                     ▼             ▼             ▼
                  Length        Entropy       Frequency
                     │             │             │
                     └─────────────┼─────────────┘
                                   ▼
                              Correlation
                                   │
                                   ▼
                              Investigation
```


