# 04 – Common Ports & Services Cheatsheet

> **Purpose:** Quick-reference guide to commonly encountered network ports and services for cybersecurity, networking, VAPT, and troubleshooting.

---

## 1. What Is a Port?

A **port** is a logical endpoint used by network applications to communicate.

Think of it like this:

* **IP address** → identifies the machine
* **Port** → identifies the service/application
* **Protocol** → defines how communication happens

### Simple Example

```text
192.168.1.10:22
```

Means:

```text
192.168.1.10 → Host
22            → Port
SSH           → Common service
TCP           → Transport protocol
```

### Important

A port being open does **not automatically mean a vulnerability exists**.

It means that something is listening or reachable on that port.

---

# 2. Port Number Ranges

TCP and UDP ports range from:

```text
0 – 65535
```

They are commonly divided into:

| Range       | Name                    | Description                       |
| ----------- | ----------------------- | --------------------------------- |
| 0–1023      | Well-known ports        | Common system/network services    |
| 1024–49151  | Registered ports        | Applications and services         |
| 49152–65535 | Dynamic/ephemeral ports | Often used temporarily by clients |

> **Note:** These ranges describe common conventions, not absolute rules for how every operating system or application behaves.

---

# 3. TCP vs UDP

A service can use TCP, UDP, or sometimes both.

## TCP

TCP is connection-oriented and provides reliable, ordered delivery.

Common examples:

```text
HTTP   → 80/TCP
HTTPS  → 443/TCP
SSH    → 22/TCP
FTP    → 21/TCP
```

## UDP

UDP is connectionless and has lower protocol overhead.

Common examples:

```text
DNS    → 53/UDP
DHCP   → 67/68 UDP
SNMP   → 161/UDP
```

Some protocols/services can use both TCP and UDP depending on the situation.

---

# 4. Most Important Ports

## 🔴 Tier 1 — Must Know

These are the ports you should know first.

| Port | Protocol | Service     | Main Purpose                                    |
| ---: | -------- | ----------- | ----------------------------------------------- |
|   20 | TCP      | FTP-Data    | FTP data connection                             |
|   21 | TCP      | FTP         | File Transfer Protocol                          |
|   22 | TCP      | SSH         | Secure remote administration                    |
|   23 | TCP      | Telnet      | Remote administration without modern encryption |
|   25 | TCP      | SMTP        | Email transfer                                  |
|   53 | TCP/UDP  | DNS         | Domain name resolution                          |
|   67 | UDP      | DHCP Server | Assigns network configuration                   |
|   68 | UDP      | DHCP Client | Receives DHCP configuration                     |
|   80 | TCP      | HTTP        | Web traffic                                     |
|  110 | TCP      | POP3        | Email retrieval                                 |
|  143 | TCP      | IMAP        | Email access                                    |
|  161 | UDP      | SNMP        | Network/device management                       |
|  443 | TCP      | HTTPS       | Encrypted web traffic                           |
|  445 | TCP      | SMB         | Windows file/printer sharing                    |
| 3389 | TCP      | RDP         | Windows remote desktop                          |

---

# 5. Web Services

## 80/TCP — HTTP

**HTTP = Hypertext Transfer Protocol**

Used for web communication.

Example:

```text
http://example.com
```

### Security Relevance

During authorized web assessments, testers may investigate:

* HTTP headers
* Authentication
* Session management
* Web application vulnerabilities
* HTTP methods
* Redirects
* Security configuration

### Check with Nmap

```bash
nmap -p 80 <authorized-target>
```

Service detection:

```bash
nmap -sV -p 80 <authorized-target>
```

---

# 443/TCP — HTTPS

HTTPS is HTTP protected using TLS.

```text
Browser
   ↓
HTTPS
   ↓
Web Server
```

HTTPS provides:

* Encryption
* Server authentication
* Integrity protection

### Security Relevance

A security assessment may examine:

* TLS configuration
* Certificate validity
* HTTP security headers
* Authentication/session behavior

Example:

```bash
nmap -sV -p 443 <authorized-target>
```

---

# 6. Remote Administration

## 22/TCP — SSH

**SSH = Secure Shell**

Used for secure remote administration.

Common uses:

```text
Remote shell
File transfer
Secure tunneling
Administration
```

Common related tools:

```bash
ssh user@host
scp file.txt user@host:/tmp/
```

### Security Relevance

Authorized testing may examine:

* Authentication configuration
* Password/key authentication
* Supported algorithms
* Access controls
* Exposed SSH service

---

# 23/TCP — Telnet

Telnet provides remote terminal access but does not provide modern encrypted communication by itself.

Example:

```bash
telnet <authorized-target> 23
```

### Security Concern

Credentials and other traffic can potentially be exposed to network interception.

For modern administration:

```text
SSH > Telnet
```

---

# 7. File Transfer

## 20/TCP — FTP Data

Used for FTP data transfer in traditional FTP operation.

## 21/TCP — FTP Control

FTP uses port 21 for the control connection.

Example:

```bash
ftp <authorized-target>
```

### Security Relevance

Authorized assessments may check:

* Anonymous access
* Authentication configuration
* Cleartext transmission
* Weak permissions
* Outdated FTP software

### Important

FTP itself does not provide encryption.

Secure alternatives include:

```text
SFTP → SSH-based file transfer
FTPS → FTP protected with TLS
```

---

# 8. DNS

## 53/UDP — DNS

**DNS = Domain Name System**

Converts domain names into network information.

Example:

```text
example.com
     ↓
DNS
     ↓
IP address
```

Typical DNS queries commonly use UDP.

## 53/TCP — DNS

TCP is also used by DNS in situations such as:

* Larger responses
* Zone transfers
* Certain DNS operations

### Useful Commands

```bash
nslookup example.com
```

Linux:

```bash
dig example.com
```

Specific record:

```bash
dig example.com A
```

### Security Relevance

Authorized DNS assessment may involve:

* Misconfigured DNS records
* Zone transfer configuration
* Subdomain discovery
* DNS security
* Unexpected exposed records

---

# 9. Email Services

## 25/TCP — SMTP

**SMTP = Simple Mail Transfer Protocol**

Primarily used for sending and relaying email.

Commonly:

```text
Mail Server → Mail Server
```

Security assessment may examine:

* Relay configuration
* Authentication
* TLS configuration
* Server information exposure

---

## 110/TCP — POP3

POP3 is used for retrieving email.

```text
Mail Server
     ↓
Email Client
```

---

## 143/TCP — IMAP

IMAP allows email clients to access and manage messages stored on a mail server.

Comparison:

| Protocol | Port | Purpose                 |
| -------- | ---: | ----------------------- |
| SMTP     |   25 | Sending/relaying mail   |
| POP3     |  110 | Retrieving mail         |
| IMAP     |  143 | Accessing/managing mail |

Encrypted variants commonly use different ports, such as:

```text
POP3S → 995
IMAPS → 993
```

---

# 10. SMB

## 445/TCP — SMB

**SMB = Server Message Block**

Commonly used for:

* Windows file sharing
* Printer sharing
* Network resources
* Windows network communication

### Security Importance

SMB is extremely important in internal network security assessments.

Potential areas of investigation include:

* SMB configuration
* Authentication
* Share permissions
* Guest/anonymous access
* Protocol versions
* Patch status

Example:

```bash
nmap -p 445 --script smb-protocols <authorized-target>
```

Only run scripts against systems you are authorized to test.

---

# 11. RDP

## 3389/TCP — RDP

**RDP = Remote Desktop Protocol**

Used for graphical remote access to Windows systems.

### Security Relevance

A security assessment may examine:

* Exposure to untrusted networks
* Authentication controls
* Network-level authentication
* Patch status
* Access control

Example:

```bash
nmap -p 3389 -sV <authorized-target>
```

---

# 12. SNMP

## 161/UDP — SNMP

**SNMP = Simple Network Management Protocol**

Used to monitor and manage network devices.

Examples:

```text
Routers
Switches
Servers
Printers
Firewalls
```

### Security Relevance

Misconfigured SNMP can potentially expose useful information about infrastructure.

Important concepts:

```text
Community strings
SNMP versions
Device information
Interfaces
System information
```

---

# 13. DHCP

DHCP automatically provides network configuration to clients.

Important ports:

| Port | Protocol | Role        |
| ---: | -------- | ----------- |
|   67 | UDP      | DHCP server |
|   68 | UDP      | DHCP client |

DHCP can provide:

* IP address
* Subnet mask
* Default gateway
* DNS server information

---

# 14. Common Database Ports

These are particularly important during internal VAPT.

|  Port | Service              | Common Database |
| ----: | -------------------- | --------------- |
|  1433 | Microsoft SQL Server | MSSQL           |
|  1521 | Oracle Database      | Oracle          |
|  3306 | MySQL                | MySQL/MariaDB   |
|  5432 | PostgreSQL           | PostgreSQL      |
|  6379 | Redis                | Redis           |
| 27017 | MongoDB              | MongoDB         |

### Important Security Concept

A database port being exposed does **not automatically mean the database is vulnerable**.

You need to determine:

```text
Is it reachable?
        ↓
What service/version?
        ↓
Is authentication required?
        ↓
Is configuration secure?
        ↓
Are known vulnerabilities applicable?
```

---

# 15. Other High-Value Ports

|         Port | Service                 | Common Usage                |
| -----------: | ----------------------- | --------------------------- |
|       69/UDP | TFTP                    | Simple file transfer        |
|  111/TCP/UDP | RPCbind                 | RPC service mapping         |
|      139/TCP | NetBIOS Session Service | Legacy Windows networking   |
|  389/TCP/UDP | LDAP                    | Directory services          |
|      636/TCP | LDAPS                   | LDAP over TLS               |
| 2049/TCP/UDP | NFS                     | Network File System         |
|     5900/TCP | VNC                     | Remote graphical access     |
|     8080/TCP | HTTP-alt                | Common alternate web port   |
|     8443/TCP | HTTPS-alt               | Common alternate HTTPS port |

---

# 16. Port → Service Memory Map

Learn these first:

```text
21    FTP
22    SSH
23    Telnet
25    SMTP
53    DNS
80    HTTP
110   POP3
139   NetBIOS
143   IMAP
161   SNMP
389   LDAP
443   HTTPS
445   SMB
636   LDAPS
1433  MSSQL
1521  Oracle
2049  NFS
3306  MySQL
3389  RDP
5432  PostgreSQL
5900  VNC
6379  Redis
8080  HTTP Alternate
27017 MongoDB
```

---

# 17. Nmap: Finding Services

## Basic Scan

```bash
nmap <authorized-target>
```

Example:

```bash
nmap scanme.nmap.org
```

---

## Service Detection

```bash
nmap -sV <authorized-target>
```

This attempts to identify:

```text
Port
State
Service
Version
```

Example conceptual output:

```text
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
```

---

# 18. Scan Specific Ports

```bash
nmap -p 22,80,443 <authorized-target>
```

Port range:

```bash
nmap -p 1-1000 <authorized-target>
```

All TCP ports:

```bash
nmap -p- <authorized-target>
```

Service detection:

```bash
nmap -sV -p 22,80,443 <authorized-target>
```

---

# 19. Understanding Nmap Output

Example:

```text
PORT    STATE    SERVICE
22/tcp  open     ssh
80/tcp  open     http
443/tcp filtered https
```

### PORT

Example:

```text
22/tcp
```

Means:

```text
22 = port
tcp = transport protocol
```

### STATE

Common states:

```text
open
closed
filtered
```

### SERVICE

Nmap's identification of the likely service associated with the port.

---

# 20. Open vs Closed vs Filtered

## Open

A service is listening and reachable.

```text
Client → Port → Service
```

## Closed

The host is reachable, but no service is listening on that port.

## Filtered

A firewall or other network filtering mechanism prevents Nmap from determining whether the port is open.

---

# 21. Pentesting Workflow

Do not think:

```text
Open port = exploit
```

Think:

```text
Discover host
      ↓
Find open ports
      ↓
Identify services
      ↓
Identify versions
      ↓
Understand configuration
      ↓
Research relevant weaknesses
      ↓
Validate safely
      ↓
Document evidence
      ↓
Recommend remediation
```

This is much closer to professional VAPT methodology.

---

# 22. Example: Port 22 Found

Suppose Nmap reports:

```text
22/tcp open ssh
```

Do not immediately assume SSH is vulnerable.

Ask:

```text
What SSH implementation?
What version?
Is authentication exposed?
What authentication methods are allowed?
Is the service intended to be Internet-facing?
Are there known applicable weaknesses?
```

---

# 23. Example: Port 445 Found

If:

```text
445/tcp open microsoft-ds
```

Think:

```text
SMB
 ↓
Windows/network file sharing
 ↓
Enumerate authorized shares/configuration
 ↓
Check authentication and permissions
 ↓
Check protocol/security configuration
 ↓
Assess relevant vulnerabilities
```

---

# 24. Example: Port 3306 Found

If:

```text
3306/tcp open mysql
```

Think:

```text
MySQL
 ↓
Is remote access intended?
 ↓
Authentication required?
 ↓
Version?
 ↓
TLS/configuration?
 ↓
Network exposure?
 ↓
Known applicable vulnerabilities?
```

---

# 25. Port Number ≠ Guaranteed Service

This is a very important professional concept.

Port numbers are conventions.

For example:

```text
8080 → commonly HTTP
```

But another application could use 8080.

Therefore:

> **Never identify a service only from the port number.**

Use service detection and application-level evidence.

Example:

```bash
nmap -sV -p 8080 <authorized-target>
```

---

# 26. TCP/UDP Scanning

TCP example:

```bash
nmap -sT -p 80 <authorized-target>
```

UDP example:

```bash
nmap -sU -p 53 <authorized-target>
```

UDP scanning can be slower and often requires different interpretation because UDP does not have a TCP-style handshake.

---

# 27. Practical Mini Lab

Use only your own machine, a deliberately vulnerable lab, or an explicitly authorized target.

### Step 1 — Check your local services

Linux:

```bash
ss -tuln
```

### Step 2 — Identify a local test server

For example, start a simple web server:

```bash
python3 -m http.server 8000
```

### Step 3 — Scan localhost

```bash
nmap -sV -p 8000 127.0.0.1
```

### Step 4 — Understand the result

You should be able to explain:

```text
127.0.0.1
   ↓
Port 8000
   ↓
TCP
   ↓
Python HTTP server
```

This teaches more than simply memorizing port numbers.

---

# 28. Common Security Mistakes

### Mistake 1

```text
Port 22 open = vulnerable
```

❌ Incorrect.

### Correct thinking

```text
Port 22 open
↓
SSH identified
↓
Version/configuration assessment
↓
Determine actual risk
```

---

### Mistake 2

Assuming:

```text
443 = automatically secure
```

❌ Incorrect.

HTTPS can still contain application vulnerabilities.

TLS protects communication; it does not automatically make the application secure.

---

### Mistake 3

Assuming:

```text
Port 80 = only web server
```

❌ Not necessarily.

A port number is not proof of the application running behind it.

---

# 29. Interview Questions

## Q1. What is a port?

A logical communication endpoint associated with a network service.

---

## Q2. How many TCP/UDP ports exist?

There are **65,536 port numbers**, numbered:

```text
0–65535
```

---

## Q3. What is port 22?

SSH.

---

## Q4. What is port 21?

FTP control connection.

---

## Q5. What is port 80?

HTTP.

---

## Q6. What is port 443?

HTTPS.

---

## Q7. What is port 53?

DNS.

---

## Q8. What is port 445?

SMB.

---

## Q9. What is port 3389?

RDP.

---

## Q10. What is port 3306?

Commonly MySQL/MariaDB.

---

## Q11. Does an open port mean a vulnerability exists?

No.

An open port indicates a reachable service; vulnerability depends on the service, version, configuration, exposure, and other factors.

---

## Q12. Why use `nmap -sV`?

To attempt service/version detection.

---

## Q13. Difference between TCP and UDP?

TCP is connection-oriented and provides reliable ordered delivery; UDP is connectionless and does not provide TCP's reliability mechanisms.

---

## Q14. What is a filtered port?

A port whose state cannot be determined because packet filtering prevents Nmap from obtaining enough information.

---

## Q15. Can HTTP run on port 8080?

Yes. Port 8080 is commonly used for alternate HTTP services, but the port number alone does not guarantee HTTP.

---

# 30. Quick Revision Table

|  Port | Service    | Protocol | Remember                  |
| ----: | ---------- | -------- | ------------------------- |
|    21 | FTP        | TCP      | File transfer             |
|    22 | SSH        | TCP      | Secure remote access      |
|    23 | Telnet     | TCP      | Legacy remote access      |
|    25 | SMTP       | TCP      | Mail transfer             |
|    53 | DNS        | TCP/UDP  | Name resolution           |
|    80 | HTTP       | TCP      | Web                       |
|   110 | POP3       | TCP      | Email retrieval           |
|   139 | NetBIOS    | TCP      | Legacy Windows networking |
|   143 | IMAP       | TCP      | Email access              |
|   161 | SNMP       | UDP      | Network management        |
|   389 | LDAP       | TCP/UDP  | Directory services        |
|   443 | HTTPS      | TCP      | TLS-protected web         |
|   445 | SMB        | TCP      | Windows file sharing      |
|   636 | LDAPS      | TCP      | LDAP over TLS             |
|  1433 | MSSQL      | TCP      | Microsoft SQL Server      |
|  1521 | Oracle     | TCP      | Oracle DB                 |
|  2049 | NFS        | TCP/UDP  | Network filesystem        |
|  3306 | MySQL      | TCP      | Database                  |
|  3389 | RDP        | TCP      | Windows remote desktop    |
|  5432 | PostgreSQL | TCP      | Database                  |
|  5900 | VNC        | TCP      | Remote desktop            |
|  6379 | Redis      | TCP      | In-memory datastore       |
|  8080 | HTTP-alt   | TCP      | Alternate web             |
| 27017 | MongoDB    | TCP      | Database                  |

---

# 31. The Golden Rule

> **Don't memorize ports only. Learn what happens behind the port.**

For every important port, learn:

```text
Port
 ↓
Protocol
 ↓
Service
 ↓
Application
 ↓
Typical configuration
 ↓
Security risks
 ↓
How to identify it
 ↓
How to secure it
```

That turns a **port-number memorization skill** into an actual **network-security skill**.

---

# Final Takeaway

Common ports are fundamental for:

* Network troubleshooting
* Nmap
* VAPT
* SOC analysis
* Incident response
* Network security
* Red-team/blue-team work
* Cybersecurity interviews

The most important first set to master is:

```text
21  FTP
22  SSH
23  Telnet
25  SMTP
53  DNS
80  HTTP
110 POP3
143 IMAP
161 SNMP
389 LDAP
443 HTTPS
445 SMB
1433 MSSQL
1521 Oracle
3306 MySQL
3389 RDP
5432 PostgreSQL
5900 VNC
6379 Redis
8080 HTTP-alt
27017 MongoDB
```

**Goal:** Don't just remember `22 = SSH`. Be able to explain **why SSH uses TCP, what an SSH service does, how Nmap identifies it, what security controls matter, and what an open SSH port means during an authorized VAPT assessment.**
