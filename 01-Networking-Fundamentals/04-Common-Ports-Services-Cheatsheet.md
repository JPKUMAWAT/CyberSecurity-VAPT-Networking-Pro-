# 1. What Is a Port?

A **port** is a logical number used by TCP or UDP to identify a network service/application endpoint.

Port range:

```text
0 ───────────── 65535
```

Think of:

```text
IP Address = Building
Port       = Room/Service entrance
Service    = What is operating there
```

Example:

```text
192.168.1.10:22
       │     │
       │     └── Port
       └──────── IP
```

Port `22` is commonly associated with SSH.

---

# 2. TCP vs UDP Ports

Ports can be used with different transport protocols.

```text
TCP
 └── 22
 └── 80
 └── 443

UDP
 └── 53
 └── 67
 └── 68
```

The same port number can technically exist independently under TCP and UDP.

For example:

```text
TCP/53
UDP/53
```

They are different transport endpoints.

---

# 3. Port Number Ranges

| Range       | Name                    | Typical use                       |
| ----------- | ----------------------- | --------------------------------- |
| 0–1023      | Well-known/System ports | Common standard services          |
| 1024–49151  | Registered ports        | Applications/services             |
| 49152–65535 | Dynamic/Ephemeral ports | Temporary client-side connections |

> Exact ephemeral ranges can vary by operating system.

---

# 4. ⭐ Most Important Ports

Memorize these first:

|    Port | Protocol | Common Service       | VAPT Importance |
| ------: | -------- | -------------------- | --------------- |
|      20 | TCP      | FTP Data             | ⭐⭐⭐             |
|      21 | TCP      | FTP Control          | ⭐⭐⭐⭐            |
|      22 | TCP      | SSH                  | ⭐⭐⭐⭐⭐           |
|      23 | TCP      | Telnet               | ⭐⭐⭐⭐⭐           |
|      25 | TCP      | SMTP                 | ⭐⭐⭐⭐            |
|      53 | TCP/UDP  | DNS                  | ⭐⭐⭐⭐⭐           |
|      67 | UDP      | DHCP Server          | ⭐⭐⭐             |
|      68 | UDP      | DHCP Client          | ⭐⭐⭐             |
|      80 | TCP      | HTTP                 | ⭐⭐⭐⭐⭐           |
|     110 | TCP      | POP3                 | ⭐⭐⭐             |
|     123 | UDP      | NTP                  | ⭐⭐⭐             |
|     135 | TCP      | MS RPC               | ⭐⭐⭐⭐            |
| 137–139 | TCP/UDP  | NetBIOS              | ⭐⭐⭐⭐            |
|     143 | TCP      | IMAP                 | ⭐⭐⭐             |
|     161 | UDP      | SNMP                 | ⭐⭐⭐⭐⭐           |
|     389 | TCP/UDP  | LDAP                 | ⭐⭐⭐⭐            |
|     443 | TCP      | HTTPS                | ⭐⭐⭐⭐⭐           |
|     445 | TCP      | SMB                  | ⭐⭐⭐⭐⭐           |
|     587 | TCP      | SMTP Submission      | ⭐⭐⭐             |
|     636 | TCP      | LDAPS                | ⭐⭐⭐             |
|     993 | TCP      | IMAPS                | ⭐⭐⭐             |
|     995 | TCP      | POP3S                | ⭐⭐⭐             |
|    1433 | TCP      | Microsoft SQL Server | ⭐⭐⭐⭐⭐           |
|    1521 | TCP      | Oracle DB            | ⭐⭐⭐⭐⭐           |
|    2049 | TCP/UDP  | NFS                  | ⭐⭐⭐⭐            |
|    3306 | TCP      | MySQL                | ⭐⭐⭐⭐⭐           |
|    3389 | TCP      | RDP                  | ⭐⭐⭐⭐⭐           |
|    5432 | TCP      | PostgreSQL           | ⭐⭐⭐⭐⭐           |
|    5900 | TCP      | VNC                  | ⭐⭐⭐⭐            |
|    6379 | TCP      | Redis                | ⭐⭐⭐⭐⭐           |
|    8080 | TCP      | Alternate HTTP/Proxy | ⭐⭐⭐⭐            |
|    8443 | TCP      | Alternate HTTPS      | ⭐⭐⭐⭐            |

---

# 5. FTP — Ports 20/21

**FTP = File Transfer Protocol**

Common ports:

```text
TCP/21 → Control
TCP/20 → Traditional active-mode data channel
```

FTP is generally **not encrypted**.

### VAPT relevance

Check for:

* Anonymous access
* Weak authentication
* Cleartext credentials
* Misconfiguration
* Outdated FTP software
* Excessive permissions

### Important

Don't assume:

```text
21 = vulnerable
```

Port 21 only tells you that something may be listening there.

---

# 6. SSH — Port 22 ⭐

**SSH = Secure Shell**

```text
TCP/22
```

Used for:

* Remote administration
* Secure shell access
* Secure file transfer mechanisms such as SFTP
* Port forwarding/tunneling

### VAPT checks

Look for:

* Weak authentication configuration
* Outdated SSH implementation
* Password authentication exposure
* Excessive user access
* Poor key management
* Internet exposure

### Common misconception

> SSH = SFTP

Not exactly.

**SFTP is a file-transfer protocol that operates over SSH.**

---

# 7. Telnet — Port 23 ⚠️

```text
TCP/23
```

Telnet provides remote terminal access but normally does not provide encryption.

### VAPT relevance

An exposed Telnet service is often a significant security concern because credentials/data may be transmitted without adequate protection.

Preferred modern alternative:

```text
Telnet → SSH
```

---

# 8. SMTP — Ports 25 / 587

**SMTP = Simple Mail Transfer Protocol**

Common ports:

```text
25  → Server-to-server SMTP / traditional SMTP
587 → Message submission
```

Also commonly seen:

```text
465 → SMTP over implicit TLS
```

### VAPT relevance

Assess:

* Open relay configuration
* Authentication configuration
* TLS configuration
* Information disclosure
* Mail-server version
* SPF/DKIM/DMARC-related security posture

---

# 9. DNS — Port 53 ⭐

**DNS = Domain Name System**

Common:

```text
UDP/53
TCP/53
```

DNS translates names into information such as IP addresses.

Example:

```text
example.com
     ↓
DNS
     ↓
IP address
```

### Why both TCP and UDP?

UDP is commonly used for normal DNS queries.

TCP can be used for situations such as:

* Large responses
* Zone transfers
* Certain DNS operations
* Reliability requirements

### VAPT relevance

Check for:

* DNS misconfiguration
* Unnecessary zone transfer exposure
* DNS version disclosure
* Poorly configured recursion
* DNS-related attack surface

---

# 10. HTTP — Port 80 ⭐

```text
TCP/80
```

HTTP is an application-layer web protocol.

Typical flow:

```text
Browser
   ↓
HTTP Request
   ↓
Server
   ↓
HTTP Response
```

Example:

```text
GET / HTTP/1.1
```

### VAPT relevance

This is one of the biggest areas of web application testing.

Look for issues such as:

* Authentication problems
* Authorization flaws
* Injection
* XSS
* Security misconfiguration
* Sensitive information exposure
* Insecure cookies
* Missing security headers

---

# 11. HTTPS — Port 443 ⭐

```text
TCP/443
```

HTTPS is HTTP protected using TLS.

Conceptually:

```text
HTTP
 ↓
TLS
 ↓
TCP
 ↓
IP
```

### Important

HTTPS provides protection for data in transit, but:

> **HTTPS does NOT automatically mean the application is secure.**

A website can use HTTPS and still have:

* SQL injection
* XSS
* Broken access control
* Authentication vulnerabilities
* SSRF
* Business-logic vulnerabilities

---

# 12. DHCP — Ports 67/68

DHCP automatically provides network configuration.

```text
UDP/67 → DHCP Server
UDP/68 → DHCP Client
```

Basic process:

```text
Client
 ↓ DHCP Discover
Server
 ↓ DHCP Offer
Client
 ↓ DHCP Request
Server
 ↓ DHCP ACK
```

Remember:

```text
67 = Server
68 = Client
```

---

# 13. POP3 — Port 110

**POP3 = Post Office Protocol version 3**

```text
TCP/110
```

Used for retrieving email.

Encrypted variant:

```text
TCP/995 → POP3S
```

---

# 14. IMAP — Port 143

**IMAP = Internet Message Access Protocol**

```text
TCP/143
```

Encrypted variant:

```text
TCP/993 → IMAPS
```

### POP3 vs IMAP

| POP3                  | IMAP              |
| --------------------- | ----------------- |
| Mainly downloads mail | Synchronizes mail |
| Simpler model         | More feature-rich |
| TCP/110               | TCP/143           |
| POP3S → 995           | IMAPS → 993       |

---

# 15. NTP — Port 123

**NTP = Network Time Protocol**

```text
UDP/123
```

Used for time synchronization.

### Why security cares

Accurate time is important for:

* Log correlation
* Incident investigation
* Authentication systems
* Kerberos
* Forensics

Incorrect time can make security investigations much harder.

---

# 16. SMB — Port 445 ⭐⭐⭐⭐⭐

**SMB = Server Message Block**

Common:

```text
TCP/445
```

Used for:

* File sharing
* Printer sharing
* Windows network services

### VAPT relevance

Check authorized systems for:

* SMB exposure
* Weak configuration
* Excessive shares
* Weak authentication
* Legacy protocol support
* Patch status
* Access-control issues

SMB is particularly important in Windows network assessments.

---

# 17. NetBIOS — Ports 137–139

Common:

```text
UDP/137 → NetBIOS Name Service
UDP/138 → NetBIOS Datagram Service
TCP/139 → NetBIOS Session Service
```

Modern Windows environments often use SMB directly over TCP/445 instead of NetBIOS session service.

### VAPT relevance

Can provide useful network enumeration information in legacy environments.

---

# 18. LDAP — Port 389

**LDAP = Lightweight Directory Access Protocol**

```text
TCP/389
```

Used for directory services.

Commonly associated with:

* Active Directory
* User accounts
* Groups
* Organizational information

Encrypted/secure variant:

```text
TCP/636 → LDAPS
```

### VAPT relevance

Directory services are extremely important during enterprise assessments.

Potential areas:

* Anonymous access
* Weak access controls
* Information disclosure
* LDAP configuration
* Authentication/security controls

---

# 19. SNMP — Port 161 ⭐⭐⭐⭐⭐

**SNMP = Simple Network Management Protocol**

Common:

```text
UDP/161
```

Used for monitoring and managing network devices.

### VAPT relevance

Misconfigured SNMP can expose:

* Device information
* Interface information
* System details
* Network information
* Configuration-related information

Important distinction:

```text
UDP/161 → SNMP queries
UDP/162 → SNMP traps
```

---

# 20. RDP — Port 3389 ⭐⭐⭐⭐⭐

**RDP = Remote Desktop Protocol**

```text
TCP/3389
```

Used for remote Windows desktop access.

Modern RDP can also use UDP for some transport functionality.

### VAPT relevance

Assess:

* Internet exposure
* Authentication controls
* MFA
* Network-level authentication
* Patch status
* Account lockout/security policies
* Access restrictions

---

# 21. Microsoft SQL Server — Port 1433

Common default:

```text
TCP/1433
```

Database service.

### VAPT relevance

Check authorized systems for:

* Unnecessary network exposure
* Authentication configuration
* Weak credentials
* Excessive privileges
* Encryption configuration
* Database version/patch level

---

# 22. Oracle Database — Port 1521

Common default:

```text
TCP/1521
```

Used by Oracle database listener services.

### VAPT relevance

Database exposure can increase attack surface.

Assess:

* Authentication
* Authorization
* Listener configuration
* Network exposure
* Patch level
* Encryption

---

# 23. NFS — Port 2049

**NFS = Network File System**

Common:

```text
TCP/2049
UDP/2049
```

Used primarily in Unix/Linux environments.

### VAPT relevance

Check for:

* Unnecessary exports
* Incorrect permissions
* Excessive network access
* Sensitive data exposure

---

# 24. MySQL — Port 3306

```text
TCP/3306
```

MySQL database server.

### VAPT relevance

Check:

* Network exposure
* Authentication
* Privileges
* Encryption
* Version/patching
* Application-to-database trust boundaries

---

# 25. PostgreSQL — Port 5432

```text
TCP/5432
```

PostgreSQL database.

### VAPT relevance

Check:

* `pg_hba.conf` access rules
* Authentication
* Network exposure
* User privileges
* TLS
* Patch level

---

# 26. Redis — Port 6379

```text
TCP/6379
```

Redis is an in-memory data store.

### VAPT relevance

An improperly exposed Redis instance can create serious security risk.

Check:

* Network exposure
* Authentication
* Access controls
* TLS
* Configuration
* Version

Never assume:

```text
6379 open = vulnerable
```

---

# 27. VNC — Port 5900

**VNC = Virtual Network Computing**

Common:

```text
TCP/5900
```

Used for remote graphical access.

### VAPT relevance

Assess:

* Authentication
* Encryption
* Exposure
* Access control
* Network restrictions

---

# 28. Alternative Web Ports

Common examples:

```text
8080
8443
8000
8008
8888
```

These can be used by:

* Development servers
* Web applications
* Proxies
* Admin interfaces
* APIs

### Important

Don't assume:

```text
8080 = HTTP
```

Port numbers are conventions.

Always identify the actual service.

---

# 29. Port ≠ Service

This is one of the **most important concepts**.

Suppose you see:

```text
443/tcp open
```

It does not automatically prove:

```text
HTTPS
```

Similarly:

```text
8080/tcp open
```

doesn't guarantee HTTP.

Think:

```text
Port number
     ↓
Common association
     ↓
Service detection
     ↓
Actual service
```

Tools such as Nmap can help identify the service.

---

# 30. VAPT Workflow Using Ports

A simple authorized workflow:

```text
1. Define Scope
      ↓
2. Discover Host
      ↓
3. Identify Open Ports
      ↓
4. Identify Services
      ↓
5. Determine Versions
      ↓
6. Research Vulnerabilities
      ↓
7. Validate Safely
      ↓
8. Document Finding
      ↓
9. Recommend Remediation
```

Example:

```text
192.168.56.10

22/tcp  → SSH
80/tcp  → HTTP
445/tcp → SMB
```

Then ask:

```text
What service?
What version?
Why is it exposed?
Who needs access?
Is configuration secure?
Is it patched?
Can network segmentation reduce exposure?
```

This is **better pentesting thinking** than simply memorizing ports.

---

# 31. Safe Nmap Practice

Use Nmap only against systems you own or have explicit permission to test.

Basic service discovery:

```bash
nmap -sV 192.168.56.10
```

Scan selected ports:

```bash
nmap -p 22,80,443 192.168.56.10
```

Scan your local lab:

```bash
nmap -sV localhost
```

Example output:

```text
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     ...
80/tcp  open  http    ...
```

### Interpretation

```text
22/tcp
 │
 ├── 22       → Port
 ├── tcp      → Transport
 ├── open     → Nmap received evidence that it is reachable/listening
 └── ssh      → Detected/associated service
```

---

# 32. Windows Commands

View listening connections:

```powershell
Get-NetTCPConnection -State Listen
```

Find a specific port:

```powershell
Get-NetTCPConnection -LocalPort 443
```

Traditional command:

```cmd
netstat -ano
```

---

# 33. Linux/Kali Commands

View listening TCP/UDP sockets:

```bash
ss -tuln
```

With process information:

```bash
sudo ss -tulnp
```

Check a specific local port:

```bash
ss -ltn 'sport = :22'
```

---

# 34. Port State Cheat Sheet

Nmap commonly reports states such as:

| State            | Meaning                                                                |
| ---------------- | ---------------------------------------------------------------------- |
| `open`           | An application appears to be accepting connections                     |
| `closed`         | Host is reachable but no application is listening                      |
| `filtered`       | Filtering prevents Nmap from determining the state                     |
| `unfiltered`     | Port is reachable but Nmap cannot determine open/closed with that scan |
| `open\|filtered` | Nmap cannot distinguish between open and filtered                      |

### Important

`open` does **not** mean:

> Vulnerable.

It means:

> There is evidence that a service is accepting connections.

---

# 35. Common Beginner Mistakes

### ❌ Mistake 1

> Port 80 always means HTTP.

No.

It's only the conventional/default association.

---

### ❌ Mistake 2

> Every open port is a vulnerability.

No.

An open port is part of the **attack surface**.

---

### ❌ Mistake 3

> Port 443 means the application is secure.

No.

HTTPS can still contain serious application vulnerabilities.

---

### ❌ Mistake 4

> TCP and UDP share exactly the same connection behavior.

No.

TCP is connection-oriented; UDP is connectionless.

---

### ❌ Mistake 5

> If a port is closed, the machine is offline.

No.

A host can be online while a particular port is closed.

---

### ❌ Mistake 6

> `filtered` means closed.

No.

A firewall/filter may be preventing the scanner from determining the true state.

---

# 36. ⭐ Must-Memorize Ports

For interviews/quizzes, start with these:

```text
21   FTP
22   SSH
23   Telnet
25   SMTP
53   DNS
67   DHCP Server
68   DHCP Client
80   HTTP
110  POP3
123  NTP
135  MS RPC
137  NetBIOS
138  NetBIOS
139  NetBIOS/SMB
143  IMAP
161  SNMP
389  LDAP
443  HTTPS
445  SMB
587  SMTP Submission
636  LDAPS
993  IMAPS
995  POP3S
1433 MSSQL
1521 Oracle
2049 NFS
3306 MySQL
3389 RDP
5432 PostgreSQL
5900 VNC
6379 Redis
8080 HTTP alternate/common proxy
8443 HTTPS alternate
```

---

# 37. Fast Memory Trick 🧠

Remember the most important group:

```text
21  FTP
22  SSH
23  Telnet

25  SMTP
53  DNS

80  HTTP
443 HTTPS

139/445 SMB

161 SNMP

389 LDAP

3389 RDP

3306 MySQL
5432 PostgreSQL
1433 MSSQL
1521 Oracle
```

---

# 38. Interview Questions

### Q1. What is a network port?

A logical transport-layer endpoint identified by a number.

### Q2. What is port 22?

Commonly SSH over TCP.

### Q3. What is port 53?

DNS, commonly using UDP and also TCP.

### Q4. What is port 443?

The conventional port for HTTPS.

### Q5. What is port 445?

SMB over TCP.

### Q6. What is port 3389?

RDP.

### Q7. What is port 3306?

Common MySQL port.

### Q8. What is port 5432?

Common PostgreSQL port.

### Q9. What is port 6379?

Common Redis port.

### Q10. What is the difference between port 80 and 443?

Both are commonly used for web traffic; 80 is conventional HTTP, while 443 is conventional HTTPS using TLS.

### Q11. Is an open port automatically vulnerable?

No.

### Q12. What does `filtered` mean in Nmap?

Filtering prevents Nmap from confidently determining the port's open/closed state.

### Q13. Why is port 445 important in Windows assessments?

It is commonly used by SMB and can expose important file-sharing and Windows network functionality.

### Q14. Why is port 161 important?

SNMP can expose useful device/network information if improperly configured.

---

# 39. Scenario-Based Questions

### Scenario 1

You find:

```text
22/tcp open
```

What should you investigate?

```text
SSH service
 ↓
Version
 ↓
Authentication configuration
 ↓
User/access exposure
 ↓
Patch level
 ↓
Network restrictions
```

Not:

> "SSH is automatically vulnerable."

---

### Scenario 2

You find:

```text
445/tcp open
```

What might this indicate?

Potential SMB exposure.

Next investigate the authorized system's:

* SMB configuration
* Shares
* Authentication
* Patch status
* Network exposure

---

### Scenario 3

You find:

```text
3306/tcp open
```

What does this tell you?

A MySQL service may be exposed, but you should verify the actual service and configuration.

---

### Scenario 4

You find:

```text
8080/tcp open
```

What should you do?

Don't immediately assume it's HTTP.

Perform service identification and determine what application is actually listening.

---

# 40. MCQ Practice

### Q1. Which port is commonly associated with SSH?

A. 21
B. 22
C. 23
D. 25

**Answer: B**

---

### Q2. DNS commonly uses:

A. 21
B. 22
C. 53
D. 80

**Answer: C**

---

### Q3. HTTPS commonly uses:

A. 80
B. 110
C. 443
D. 445

**Answer: C**

---

### Q4. SMB commonly uses:

A. 25
B. 53
C. 139/445
D. 3389

**Answer: C**

---

### Q5. RDP commonly uses:

A. 3306
B. 3389
C. 5432
D. 5900

**Answer: B**

---

### Q6. MySQL commonly uses:

A. 1433
B. 1521
C. 3306
D. 5432

**Answer: C**

---

### Q7. PostgreSQL commonly uses:

A. 3306
B. 3389
C. 5432
D. 6379

**Answer: C**

---

### Q8. Redis commonly uses:

A. 389
B. 636
C. 6379
D. 8080

**Answer: C**

---

### Q9. SNMP commonly uses:

A. UDP/53
B. UDP/123
C. UDP/161
D. TCP/443

**Answer: C**

---

### Q10. LDAP commonly uses:

A. 389
B. 443
C. 445
D. 636

**Answer: A**

---

### Q11. LDAPS commonly uses:

A. 389
B. 636
C. 993
D. 995

**Answer: B**

---

### Q12. Which port is commonly associated with HTTP?

A. 22
B. 53
C. 80
D. 443

**Answer: C**

---

### Q13. Which port is commonly associated with SMTP?

A. 25
B. 53
C. 110
D. 143

**Answer: A**

---

### Q14. Which is the common SMTP submission port?

A. 21
B. 25
C. 587
D. 995

**Answer: C**

---

### Q15. What does an Nmap `filtered` state generally mean?

A. Definitely vulnerable
B. Definitely closed
C. A filtering mechanism prevents confident determination
D. The host is offline

**Answer: C**

---

# 41. Practical Labs 🧪

Only use systems you own or have explicit authorization to test.

## Lab 1 — Local Port Discovery

On Kali:

```bash
ss -tuln
```

Identify:

```text
Port
Protocol
Listening state
```

---

## Lab 2 — Local Nmap

Run:

```bash
nmap -sV localhost
```

Create a table:

| Port | State | Service | Version |
| ---- | ----- | ------- | ------- |
|      |       |         |         |

---

## Lab 3 — Start a Local Web Server

In a safe local directory:

```bash
python3 -m http.server 8000
```

Then check:

```bash
ss -ltn
```

You should see a listening service on port `8000`.

This teaches an important lesson:

> **The port number doesn't define the service; the application listening on the port does.**

---

## Lab 4 — Wireshark

Capture your own local HTTP traffic and identify:

```text
Ethernet
 ↓
IP
 ↓
TCP
 ↓
HTTP
```

---

# 42. Real VAPT Mental Model

Don't think:

```text
Port → Vulnerability
```

Think:

```text
Port
 ↓
Service
 ↓
Version
 ↓
Configuration
 ↓
Exposure
 ↓
Attack Surface
 ↓
Potential Vulnerability
 ↓
Safe Validation
 ↓
Risk
 ↓
Remediation
```

Example:

```text
445/tcp
   ↓
SMB
   ↓
Version/configuration
   ↓
Is it unnecessarily exposed?
   ↓
Are shares properly restricted?
   ↓
Is it patched?
   ↓
What is the business impact?
```

This is the mindset you want to develop as a VAPT learner.

---

# 43. Final Mental Model 🧠

Remember:

```text
IP Address
    +
Transport Protocol
    +
Port
    ↓
Network Endpoint
    ↓
Service
    ↓
Application
```

For example:

```text
192.168.1.50:443/TCP
       ↓
     HTTPS?
       ↓
Verify service
       ↓
Identify version/configuration
       ↓
Assess security
```

**Ports tell you where to look.
Service detection tells you what is there.
VAPT determines whether what is there creates security risk.**

---

# 44. Final Cheat Sheet

```text
21    FTP
22    SSH
23    Telnet
25    SMTP
53    DNS
67    DHCP Server
68    DHCP Client
80    HTTP
110   POP3
123   NTP
135   MS RPC
137   NetBIOS
138   NetBIOS
139   NetBIOS Session
143   IMAP
161   SNMP
389   LDAP
443   HTTPS
445   SMB
587   SMTP Submission
636   LDAPS
993   IMAPS
995   POP3S
1433  MSSQL
1521  Oracle
2049  NFS
3306  MySQL
3389  RDP
5432  PostgreSQL
5900  VNC
6379  Redis
8080  Alternate HTTP/common proxy
8443  Alternate HTTPS
```

## Key Takeaways

* A **port is an endpoint**, not a vulnerability.
* TCP and UDP have separate port spaces.
* Port numbers are conventions; **verify the actual service**.
* `22 → SSH`, `53 → DNS`, `80 → HTTP`, `443 → HTTPS`, `445 → SMB`, `3389 → RDP` are high-priority memories.
* Database ports such as `3306`, `5432`, `1433`, and `1521` are important during authorized internal assessments.
* `161/UDP` is important for SNMP.
* `389/636` are important for LDAP/LDAPS.
* Nmap helps move from **port → service → version**.
* An open port represents **attack surface**, not automatically a vulnerability.
* Always perform testing within your authorized scope.
