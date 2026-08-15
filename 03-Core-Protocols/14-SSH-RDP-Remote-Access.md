

# 1. What Is Remote Access?

Remote access means controlling or interacting with a computer over a network without physically being in front of it.

Example:

```text
Your Computer
     │
     │ Network
     ▼
┌──────────────┐
│ Remote Server│
│              │
│ Linux/Windows│
└──────────────┘
```

Two important remote-access protocols are:

| Protocol |  Common Port | Typical Platform | Main Purpose               |
| -------- | -----------: | ---------------- | -------------------------- |
| SSH      |       TCP 22 | Linux/Unix       | Secure command-line access |
| RDP      | TCP/UDP 3389 | Windows          | Remote graphical desktop   |

---

# 2. SSH — Secure Shell

## Definition

**SSH (Secure Shell)** is a cryptographic network protocol used for secure remote administration and communication.

It commonly provides:

* Remote terminal access
* Secure file transfer
* Port forwarding
* Authentication
* Encrypted communication

Typical architecture:

```text
SSH Client                         SSH Server
┌─────────────┐                  ┌─────────────┐
│ Kali Linux  │ ─── TCP 22 ────► │ Linux       │
│ ssh command │                  │ sshd        │
└─────────────┘                  └─────────────┘
```

---

# 3. SSH Components

SSH generally uses a **client-server architecture**.

### SSH Client

The client initiates the connection.

Examples:

```bash
ssh user@192.168.1.10
```

### SSH Server

The server listens for incoming SSH connections.

The server-side daemon is commonly called:

```text
sshd
```

Check SSH service on Linux:

```bash
systemctl status ssh
```

---

# 4. SSH Default Port

SSH traditionally uses:

```text
TCP/22
```

However:

> **Port 22 is a default, not a requirement.**

An administrator can configure SSH to listen on another port.

Therefore:

```text
Port 22 open
        ↓
Potential SSH service
        ↓
Verify service/version
```

Do not assume that every service on port 22 is necessarily SSH.

---

# 5. How SSH Connection Works

A simplified SSH connection:

```text
1. Client connects
       ↓
2. TCP connection established
       ↓
3. SSH protocol negotiation
       ↓
4. Server authentication
       ↓
5. Key exchange
       ↓
6. User authentication
       ↓
7. Encrypted session
```

The important idea is:

> SSH establishes cryptographic protection before normal interactive communication occurs.

---

# 6. SSH Encryption

SSH protects communication using cryptographic mechanisms.

It provides:

### Confidentiality

Attackers should not be able to simply read the encrypted session.

### Integrity

Changes to transmitted data should be detectable.

### Authentication

The client can authenticate the server, and the user can authenticate to the server.

---

# 7. SSH Host Keys

One important SSH concept is the **host key**.

The server has cryptographic host keys that help the client identify the server.

When connecting to a server for the first time, SSH may show something similar to:

```text
The authenticity of host 'server' can't be established.
Are you sure you want to continue connecting?
```

The user should verify the server's fingerprint through a trusted channel when possible.

### Why?

Without verification, users can potentially accept a malicious server during a man-in-the-middle scenario.

---

# 8. SSH Authentication Methods

Common authentication methods include:

### 1. Password Authentication

```text
Username
   +
Password
   ↓
SSH Server
```

Simple, but vulnerable to:

* Weak passwords
* Password reuse
* Credential attacks

---

### 2. Public-Key Authentication

Uses a key pair:

```text
Private Key ─────► kept secret
Public Key  ─────► installed on server
```

The private key should **never be shared**.

Typical Linux location:

```text
~/.ssh/
```

Common key files include:

```text
id_ed25519
id_ed25519.pub
```

---

# 9. SSH Key Authentication Flow

Simplified:

```text
Client                         Server

Private Key                    Public Key
    │                              │
    │──── Authentication ─────────►│
    │                              │
    │◄── Cryptographic challenge ──│
    │                              │
    │──── Proof of private key ───►│
    │                              │
    │──── Access granted ─────────►│
```

The server does not need the user's private key.

---

# 10. SSH Basic Commands

Connect to an authorized server:

```bash
ssh username@SERVER_IP
```

Specify a different port:

```bash
ssh -p 2222 username@SERVER_IP
```

Use a specific private key:

```bash
ssh -i ~/.ssh/id_ed25519 username@SERVER_IP
```

Verbose troubleshooting:

```bash
ssh -v username@SERVER_IP
```

More detailed debugging:

```bash
ssh -vvv username@SERVER_IP
```

> Use these only against systems you own or are explicitly authorized to test.

---

# 11. SSH File Transfer

SSH also supports secure file transfer mechanisms.

## SCP

Example:

```bash
scp report.txt username@SERVER_IP:/tmp/
```

Download:

```bash
scp username@SERVER_IP:/tmp/report.txt .
```

## SFTP

SFTP provides an interactive file-transfer interface over SSH.

```bash
sftp username@SERVER_IP
```

---

# 12. SSH Port Forwarding

SSH can transport network connections through an encrypted SSH connection.

Three important concepts:

### Local Forwarding

```text
Client → SSH Server → Internal Service
```

### Remote Forwarding

```text
Remote System → SSH Server → Client-side destination
```

### Dynamic Forwarding

SSH can provide a SOCKS proxy.

```text
Application
     ↓
SOCKS Proxy
     ↓
SSH Tunnel
     ↓
Remote Network
```

Port forwarding is extremely important in legitimate administration and authorized penetration testing because it can provide controlled access to services that are otherwise unreachable.

---

# 13. SSH Security Risks

Common SSH security issues include:

### Weak passwords

```text
Weak credentials
       ↓
Unauthorized access
```

### Password authentication unnecessarily exposed

Organizations may prefer stronger authentication controls depending on their environment.

### Exposed SSH service

Internet-facing SSH increases attack surface.

### Outdated SSH software

Old versions may contain known vulnerabilities.

### Poor key management

Examples:

* Private keys exposed
* Excessive permissions
* Shared accounts
* Forgotten authorized keys

### Excessive privileges

A compromised SSH account with unnecessary administrative privileges increases impact.

---

# 14. SSH VAPT Checklist

During an authorized assessment, evaluate:

```text
SSH
│
├── Is service exposed?
├── Is exposure required?
├── Software/version
├── Authentication methods
├── Password policy
├── Public-key configuration
├── Root login policy
├── User privileges
├── SSH configuration
├── Cryptographic algorithms
├── Logging
└── Patch level
```

Useful defensive inspection:

```bash
ss -tulnp
```

Check SSH configuration:

```bash
sshd -T
```

On many Linux systems, the configuration file is:

```text
/etc/ssh/sshd_config
```

---

# 15. RDP — Remote Desktop Protocol

## Definition

**RDP (Remote Desktop Protocol)** is a Microsoft protocol that allows a user to interact with a remote Windows desktop.

Unlike SSH, RDP normally provides a **graphical desktop session**.

```text
┌──────────────┐
│ RDP Client   │
│ Windows/Linux│
└──────┬───────┘
       │
       │ RDP
       ▼
┌──────────────┐
│ Windows      │
│ Remote Host  │
└──────────────┘
```

---

# 16. RDP Default Port

Common RDP ports:

```text
TCP 3389
UDP 3389
```

Modern RDP can use both TCP and UDP depending on configuration and network conditions.

Again:

> **3389 is the default, not a guarantee.**

---

# 17. RDP vs SSH

| Feature               | SSH           | RDP                                      |
| --------------------- | ------------- | ---------------------------------------- |
| Full name             | Secure Shell  | Remote Desktop Protocol                  |
| Common platform       | Linux/Unix    | Windows                                  |
| Default port          | TCP 22        | TCP/UDP 3389                             |
| Interface             | Command line  | Graphical                                |
| Encryption            | Yes           | Yes                                      |
| File transfer         | SCP/SFTP      | Clipboard/drive/printer redirection etc. |
| Port forwarding       | Yes           | Different mechanisms                     |
| Common administration | Linux servers | Windows desktops/servers                 |

---

# 18. How RDP Works — Simplified

```text
RDP Client
    │
    │ Connection
    ▼
Windows RDP Service
    │
    ├── Authentication
    │
    ├── Session establishment
    │
    ├── Graphics/input communication
    │
    └── Remote desktop
```

The user can interact with:

* Mouse
* Keyboard
* Applications
* Files
* Desktop environment

depending on permissions and configuration.

---

# 19. RDP Authentication

RDP commonly integrates with Windows authentication mechanisms.

A simplified flow:

```text
RDP Client
    │
    ▼
Authentication
    │
    ▼
Windows Security
    │
    ▼
Credentials validated
    │
    ▼
Desktop session
```

---

# 20. NLA — Network Level Authentication

**NLA (Network Level Authentication)** requires authentication earlier in the RDP connection process rather than allowing a full desktop session to be established first.

Benefits include:

* Reducing unnecessary resource usage
* Reducing exposure of the full RDP session
* Providing an additional security boundary

For modern Windows environments:

> NLA should generally remain enabled unless there is a justified compatibility reason not to use it.

---

# 21. RDP Security Risks

Common risks include:

### 1. Internet-exposed RDP

```text
Internet
   │
   ▼
RDP 3389
   │
   ▼
Windows Host
```

Direct exposure increases attack surface.

### 2. Weak credentials

Compromised credentials can lead to remote access.

### 3. Missing patches

RDP-related vulnerabilities have historically had serious consequences.

### 4. Excessive privileges

An RDP account with administrator privileges can significantly increase impact.

### 5. Poor network segmentation

If RDP is reachable from too many network segments:

```text
Compromised Host
      ↓
RDP
      ↓
Many Internal Hosts
```

This can increase lateral-movement opportunities.

---

# 22. RDP VAPT Checklist

During authorized testing:

```text
RDP
│
├── Is RDP exposed?
├── Is Internet access required?
├── TCP/UDP exposure
├── NLA enabled?
├── Authentication controls
├── Account lockout controls
├── Patch level
├── Windows configuration
├── User privileges
├── Network segmentation
├── Logging/monitoring
└── MFA or gateway controls where applicable
```

---

# 23. SSH vs RDP — Security Perspective

Think about the attack surface:

```text
SSH
 │
 ├── Authentication
 ├── Keys/passwords
 ├── SSH configuration
 ├── Cryptography
 ├── User privileges
 └── Network exposure

RDP
 │
 ├── Authentication
 ├── NLA
 ├── Windows configuration
 ├── Patch level
 ├── User privileges
 └── Network exposure
```

The protocol itself is only one part of security.

A secure protocol can still be deployed insecurely.

---

# 24. Service Enumeration

Suppose an authorized lab host is:

```text
192.168.56.10
```

A basic service-discovery command is:

```bash
nmap -sV -p 22,3389 192.168.56.10
```

Possible conceptual result:

```text
PORT     STATE    SERVICE
22/tcp   open     ssh
3389/tcp open     ms-wbt-server
```

Interpretation:

```text
22
 ↓
SSH
 ↓
Linux/Unix administration possible

3389
 ↓
RDP
 ↓
Windows remote desktop possible
```

This does **not** automatically mean the service is vulnerable.

---

# 25. VAPT Thinking: Open Port ≠ Vulnerability

This is extremely important.

```text
Open Port
    ↓
Service
    ↓
Version
    ↓
Configuration
    ↓
Authentication
    ↓
Known vulnerabilities
    ↓
Impact
```

For example:

```text
3389 open
   ↓
RDP detected
   ↓
NLA enabled?
   ↓
Patch level?
   ↓
Authentication secure?
   ↓
Network exposure?
   ↓
Risk assessment
```

---

# 26. Attack Surface Concept

An exposed remote-access service increases attack surface.

Example:

```text
Internet
   │
   ├── HTTPS
   ├── DNS
   └── SSH ← remote administration
             │
             ▼
          Server
```

Security teams should ask:

> "Does this service need to be reachable from this network?"

Not simply:

> "Is the service secure?"

---

# 27. Network Segmentation

Good architecture often restricts administrative protocols.

Instead of:

```text
Internet
   │
   ▼
SSH/RDP
   │
   ▼
Production Server
```

A more controlled architecture could be:

```text
Internet
   │
   ▼
VPN / Zero-Trust Access
   │
   ▼
Admin Network
   │
   ├── SSH
   └── RDP
        │
        ▼
   Internal Servers
```

This reduces unnecessary exposure.

---

# 28. Logging & Monitoring

Remote access should generate useful security logs.

For SSH, administrators can investigate:

* Successful logins
* Failed logins
* Source IPs
* User accounts
* Authentication methods

For Windows/RDP, administrators can investigate:

* Successful logons
* Failed logons
* Account activity
* Source information
* Remote-session events

These logs can feed:

```text
Host Logs
    ↓
SIEM
    ↓
Detection Rules
    ↓
Alert
    ↓
Security Team
```

---

# 29. SSH/RDP and Incident Response

Suppose an account suddenly logs in from an unusual source.

Security workflow:

```text
Login Event
    ↓
Log Collection
    ↓
SIEM Detection
    ↓
Analyst Investigation
    ↓
Source IP + Account
    ↓
Validate Activity
    ↓
Contain if malicious
```

Therefore remote-access protocols are important not only for VAPT but also for blue-team security.

---

# 30. Common Beginner Mistakes

### ❌ Mistake 1

Thinking:

```text
22 = always SSH
```

Correct:

```text
22 = commonly SSH
```

Always verify the service.

---

### ❌ Mistake 2

Thinking:

```text
3389 open = vulnerable
```

Incorrect.

An open port simply indicates that a service may be reachable.

---

### ❌ Mistake 3

Confusing SSH with SFTP.

SSH:

```text
Secure remote shell
```

SFTP:

```text
File transfer subsystem over SSH
```

---

### ❌ Mistake 4

Thinking RDP is only TCP.

Modern RDP deployments can use both:

```text
TCP
UDP
```

depending on configuration.

---

### ❌ Mistake 5

Ignoring privileges.

A secure authentication mechanism doesn't prevent damage if a highly privileged account is compromised.

---

# 31. Practical Lab — SSH

Use a Linux VM that you own.

### Step 1 — Check SSH service

```bash
systemctl status ssh
```

### Step 2 — Check listening socket

```bash
ss -lntp | grep :22
```

### Step 3 — Find your IP

```bash
ip addr
```

### Step 4 — Connect from another authorized machine

```bash
ssh username@LAB_IP
```

### Step 5 — Check identity

```bash
whoami
```

### Step 6 — Exit

```bash
exit
```

---

# 32. Practical Lab — RDP

Use a Windows VM that you own or are authorized to test.

Architecture:

```text
Kali / Linux
     │
     │ RDP
     ▼
Windows VM
```

Practice:

1. Enable RDP on the lab Windows machine.
2. Restrict access to the lab network.
3. Verify the RDP service.
4. Connect using an RDP client.
5. Check Windows security logs.
6. Disable RDP when the lab is complete.

---

# 33. Mini VAPT Scenario

You perform an authorized scan:

```text
22/tcp   open   ssh
3389/tcp open   rdp
```

### Question 1

Should you immediately report both as critical vulnerabilities?

**No.**

You need more information.

---

### Next questions:

```text
SSH
├── Version?
├── Authentication?
├── Root login?
├── Exposure?
├── Patch level?
└── User privileges?

RDP
├── Version?
├── NLA?
├── Exposure?
├── Patch level?
├── Authentication?
└── Network segmentation?
```

Then determine:

```text
Likelihood × Impact
        ↓
Risk
```

---

# 34. Interview Questions

### Q1. What is SSH?

A secure protocol for remote administration and communication, commonly used on Linux/Unix systems.

### Q2. What is the default SSH port?

TCP 22.

### Q3. What is RDP?

Microsoft's Remote Desktop Protocol for graphical remote access to Windows systems.

### Q4. What is the common RDP port?

TCP/UDP 3389.

### Q5. What is `sshd`?

The SSH server daemon/process.

### Q6. What is SSH public-key authentication?

An authentication method using a public/private cryptographic key pair.

### Q7. Where should the SSH private key be kept?

Only by the authorized owner/client and protected from unauthorized access.

### Q8. What is NLA?

Network Level Authentication, which authenticates before establishing a full RDP session.

### Q9. Is an open SSH port automatically a vulnerability?

No.

### Q10. Why is Internet-facing RDP risky?

It increases the exposed attack surface and makes authentication and service security especially important.

---

# 35. Scenario-Based Interview Questions

### Scenario 1

You find:

```text
22/tcp open ssh
```

What do you do next?

**Answer:**

Identify the service/version, understand authentication and configuration, determine exposure, check patch status, and assess risk within authorization.

---

### Scenario 2

RDP is exposed to the Internet.

What security recommendation would you consider?

**Answer:**

Restrict direct Internet exposure where possible and use controlled access such as VPN/zero-trust access, strong authentication, NLA, patching, monitoring, and network restrictions.

---

### Scenario 3

A user has SSH access but also unnecessary administrative privileges.

Why is this dangerous?

**Answer:**

Compromise of that account could provide much greater impact because the attacker inherits the account's excessive privileges.

---

# 36. MCQs

### Q1. SSH commonly uses which port?

A. 21
B. 22
C. 80
D. 443

**Answer: B**

---

### Q2. RDP is primarily associated with:

A. Linux DNS
B. Windows remote desktop
C. Email transfer
D. Web browsing

**Answer: B**

---

### Q3. Which is commonly associated with RDP?

A. 22
B. 53
C. 3389
D. 445

**Answer: C**

---

### Q4. What does `sshd` refer to?

A. DNS client
B. SSH server daemon
C. HTTP server
D. Firewall

**Answer: B**

---

### Q5. Which provides a graphical remote desktop?

A. SSH
B. DNS
C. RDP
D. SMTP

**Answer: C**

---

### Q6. Which SSH authentication method uses a key pair?

A. Public-key authentication
B. DNS authentication
C. HTTP authentication
D. MAC address authentication

**Answer: A**

---

### Q7. Is port 22 guaranteed to be SSH?

A. Yes
B. No

**Answer: B**

---

### Q8. What does NLA stand for?

A. Network Level Authentication
B. Network Login Access
C. New Linux Authentication
D. Network Layer Authorization

**Answer: A**

---

### Q9. What should a pentester do after discovering an open remote-access port?

A. Assume it is vulnerable
B. Immediately exploit it
C. Enumerate and assess the service/configuration
D. Ignore it

**Answer: C**

---

### Q10. Which is a major SSH security concern?

A. Strong cryptography
B. Weak credentials
C. Proper patching
D. Network segmentation

**Answer: B**

---

# 37. Must Remember 🧠

```text
SSH
↓
Secure Shell
↓
Common TCP port 22
↓
Usually CLI
↓
Linux/Unix administration
↓
Password / key authentication
```

```text
RDP
↓
Remote Desktop Protocol
↓
Common TCP/UDP port 3389
↓
Graphical desktop
↓
Windows
↓
NLA + authentication + patching + segmentation
```

---

# 38. Quick Cheat Sheet

| Concept          | SSH                                 | RDP                                             |
| ---------------- | ----------------------------------- | ----------------------------------------------- |
| Full name        | Secure Shell                        | Remote Desktop Protocol                         |
| Typical platform | Linux/Unix                          | Windows                                         |
| Common port      | TCP 22                              | TCP/UDP 3389                                    |
| Interface        | CLI                                 | GUI                                             |
| Authentication   | Password / keys etc.                | Windows authentication mechanisms               |
| Encryption       | Yes                                 | Yes                                             |
| File transfer    | SCP/SFTP                            | Redirection/features depending on configuration |
| Major risk       | Weak credentials/keys/configuration | Weak credentials/exposure/patch/configuration   |
| VAPT focus       | Exposure + auth + keys + config     | Exposure + NLA + auth + patching                |

---

# 39. Final Mental Model

Don't memorize only:

```text
22 = SSH
3389 = RDP
```

Think like a pentester:

```text
        Remote Access Service
                 │
                 ▼
            Is it exposed?
                 │
                 ▼
          Identify service
                 │
                 ▼
          Identify version
                 │
                 ▼
       Check configuration
                 │
                 ▼
       Check authentication
                 │
                 ▼
        Check privileges
                 │
                 ▼
       Check network exposure
                 │
                 ▼
       Check patch/vulnerabilities
                 │
                 ▼
          Assess impact
                 │
                 ▼
             Report
```

That is the difference between **memorizing ports** and actually understanding remote-access security.

---

# Key Takeaways

* **SSH** provides secure remote command-line access and commonly uses TCP 22.
* **RDP** provides graphical remote access to Windows and commonly uses TCP/UDP 3389.
* Default ports are conventions, **not proof of service identity**.
* Open ports are **attack-surface indicators**, not automatically vulnerabilities.
* SSH security depends heavily on authentication, key management, configuration, privileges, patching, and exposure.
* RDP security depends heavily on NLA, authentication, patching, network restrictions, privileges, and monitoring.
* Remote administration should ideally be restricted to trusted management paths.
* VAPT should move from **discovery → enumeration → configuration analysis → risk assessment → reporting**.
* Logging and monitoring are critical for detecting suspicious remote access.

# Practical Projects

### 🟢 Project 1 — SSH Lab

Build two Linux VMs:

```text
Kali ───── SSH ───── Linux Server
```

Practice:

* SSH connection
* Key authentication
* Service identification
* SSH configuration review
* Authentication logs

### 🟡 Project 2 — RDP Security Lab

Build:

```text
Kali/Linux ───── RDP ───── Windows VM
```

Study:

* RDP exposure
* NLA
* Windows authentication
* Firewall rules
* Security logs
* Network segmentation

### 🔴 Project 3 — Remote Access VAPT Report

Create an authorized lab report containing:

```text
Target
 ↓
22/SSH
 ↓
3389/RDP
 ↓
Service/version
 ↓
Configuration
 ↓
Authentication
 ↓
Exposure
 ↓
Risk
 ↓
Remediation
```

Do **not** test random Internet hosts; use your own lab or an explicitly authorized target.

---
