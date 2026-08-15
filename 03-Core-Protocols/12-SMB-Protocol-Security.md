

# 1. What is SMB?

**SMB (Server Message Block)** is a network protocol used primarily for sharing:

* Files
* Folders
* Printers
* Named pipes
* Other network resources

It is heavily associated with **Windows networks**, although Linux systems can also provide SMB services using **Samba**.

### Simple Example

Imagine:

```text
Windows PC A
     |
     | SMB
     ↓
Windows PC B
     |
     └── Shared Folder
         ├── report.pdf
         ├── project.docx
         └── backup.zip
```

PC A can access the shared folder on PC B through SMB, assuming the appropriate permissions are available.

---

# 2. SMB in a Network

A typical SMB connection looks like:

```text
Client
  |
  | TCP connection
  ↓
Server
  |
  └── SMB service
       |
       ├── Authentication
       ├── Share access
       ├── File operations
       └── Permissions
```

The important idea is:

> **SMB provides the communication protocol; authentication and filesystem/share permissions determine what the user can actually access.**

---

# 3. SMB Ports

These ports are extremely important during network enumeration.

|        Port | Protocol / Usage                     | Importance |
| ----------: | ------------------------------------ | ---------- |
| **445/TCP** | SMB over TCP / Direct Hosting of SMB | ⭐⭐⭐⭐⭐      |
| **139/TCP** | SMB over NetBIOS Session Service     | ⭐⭐⭐⭐       |
|     137/UDP | NetBIOS Name Service                 | ⭐⭐⭐        |
|     138/UDP | NetBIOS Datagram Service             | ⭐⭐⭐        |

### Must Remember

```text
445 → SMB
139 → SMB over NetBIOS
```

If you see:

```text
445/tcp open microsoft-ds
```

SMB should immediately come to mind.

---

# 4. SMB Versions

SMB has evolved considerably.

| Version     | General Era                                              |
| ----------- | -------------------------------------------------------- |
| SMB1 / CIFS | Older/legacy                                             |
| SMB 2.x     | Modernized SMB                                           |
| SMB 3.x     | Modern SMB with additional security/performance features |

### SMB1

SMB1 is an old protocol version and has been associated with serious security problems.

Modern environments generally prefer newer SMB versions.

### Security Principle

> **Disable legacy protocols when they are unnecessary.**

---

# 5. SMB vs Samba

These terms are often confused.

### SMB

The **protocol**.

### Samba

An open-source software suite that implements SMB functionality, particularly on Linux/Unix systems.

For example:

```text
Linux Server
     |
     └── Samba
          |
          └── SMB
               |
               └── Windows Client
```

So:

> **SMB = protocol**
> **Samba = implementation/software**

---

# 6. What is an SMB Share?

An SMB share is a network-accessible resource.

Example:

```text
\\SERVER01\Public
```

Here:

```text
SERVER01 → Server
Public    → Share
```

Another example:

```text
\\192.168.1.20\Documents
```

This is commonly called a **UNC path**.

### UNC

**Universal Naming Convention**

Typical format:

```text
\\server\share
```

---

# 7. Common SMB Shares

You may encounter shares such as:

```text
ADMIN$
C$
IPC$
NETLOGON
SYSVOL
Public
Documents
Backup
```

Not every share should be assumed vulnerable.

For example:

### ADMIN$

Administrative share.

### C$

Administrative share exposing the C: drive to authorized administrators.

### IPC$

Used for inter-process communication and other Windows networking functionality.

### NETLOGON / SYSVOL

Commonly important in Windows Active Directory environments.

---

# 8. SMB Authentication

SMB can require authentication before allowing access to protected resources.

Conceptually:

```text
Client
  |
  | "I want \\SERVER\Share"
  ↓
SMB Server
  |
  | Authentication required
  ↓
Client provides credentials
  |
  ↓
Server checks identity
  |
  ├── Authentication succeeds
  |       ↓
  |    Authorization check
  |
  └── Authentication fails
```

Two concepts must be separated:

### Authentication

**Who are you?**

### Authorization

**What are you allowed to access?**

This distinction is extremely important in VAPT.

---

# 9. NTLM and Kerberos

In Windows environments, you may encounter authentication mechanisms such as:

* NTLM
* Kerberos

### NTLM

An older Windows authentication protocol still encountered in many environments.

### Kerberos

The primary authentication protocol used by modern Windows Active Directory environments.

### Important

Do not think:

```text
SMB = NTLM
```

That's incorrect.

SMB is the protocol providing file/resource sharing; authentication can involve different mechanisms depending on the environment and configuration.

---

# 10. SMB Permissions

SMB security can involve multiple permission layers.

A useful mental model is:

```text
User
 ↓
Share Permission
 ↓
NTFS Permission
 ↓
Effective Access
```

For example:

```text
Share permission:
Read

NTFS permission:
Modify

Effective network access:
Read
```

The effective permissions depend on the combination of applicable controls.

### Important Principle

> The most restrictive applicable permission generally limits the resulting access.

---

# 11. Why SMB is Important in VAPT

SMB can expose a significant network attack surface.

During an authorized assessment, a pentester may investigate:

```text
SMB detected
     ↓
Identify SMB version
     ↓
Enumerate shares
     ↓
Determine authentication requirements
     ↓
Review permissions
     ↓
Check configuration
     ↓
Identify known vulnerabilities
     ↓
Assess impact
```

---

# 12. SMB Enumeration

Enumeration means collecting information about the SMB service.

Potential information includes:

* Hostname
* Domain/workgroup
* SMB version
* Available shares
* Share permissions
* Server information
* Authentication configuration
* Security settings

### Kali Example

For an **authorized lab target**:

```bash
nmap -p 139,445 <LAB-IP>
```

This first answers:

> Are the common SMB ports exposed?

---

# 13. Nmap SMB Enumeration

Nmap has SMB-related NSE scripts.

Example:

```bash
nmap -p445 --script smb-protocols <LAB-IP>
```

This can help identify supported SMB protocol versions.

Another useful lab command:

```bash
nmap -p445 --script smb2-security-mode <LAB-IP>
```

This can provide information about SMB2 security configuration.

### Important

Don't blindly run every NSE script against systems you don't own or have permission to test.

---

# 14. smbclient

On Kali/Linux, `smbclient` can interact with SMB shares.

Check the help:

```bash
smbclient --help
```

In an authorized lab, share enumeration can be performed with:

```bash
smbclient -L //<LAB-IP>/ -N
```

Here:

```text
-L → list shares
-N → don't request a password interactively
```

If anonymous access is not permitted, the server may reject the request.

---

# 15. Connecting to an SMB Share

In a lab where you have authorized credentials:

```bash
smbclient //<LAB-IP>/Public -U username
```

The general idea is:

```text
smbclient
    ↓
SMB Server
    ↓
Authentication
    ↓
Share
    ↓
Files/directories
```

Once connected, common `smbclient` operations include:

```text
ls
cd
get
put
pwd
```

These resemble familiar filesystem commands.

---

# 16. Anonymous SMB Access

One important security issue is **unnecessary anonymous/guest access**.

Potentially:

```text
Unauthenticated user
        |
        ↓
      SMB
        |
        ↓
   Public Share
        |
        ├── Internal documents
        ├── Backups
        └── Configuration files
```

This can cause information disclosure.

### But:

Anonymous access itself is not automatically a vulnerability.

The question is:

> **What sensitive resources become accessible without appropriate authentication?**

---

# 17. SMB Security Misconfigurations

Common issues include:

### 1. Anonymous/Guest Access

Unauthenticated users can access sensitive shares.

### 2. Excessive Permissions

Users receive more access than necessary.

### 3. Sensitive Files

Shares contain:

```text
backup.zip
config.txt
credentials.txt
database backups
internal documents
```

### 4. Legacy SMB Versions

Especially unnecessary SMB1 exposure.

### 5. Weak Authentication Configuration

Poorly configured authentication can increase attack risk.

### 6. Exposed SMB to Untrusted Networks

Internet-facing SMB is generally a major security concern.

---

# 18. SMB and Information Disclosure

Suppose an authorized assessment finds:

```text
\\SERVER\Public

files:
├── company.pdf
├── backup.zip
├── config.ini
└── old_users.csv
```

Even without exploiting anything, this may reveal:

* Employee names
* Internal hostnames
* Network structure
* Software versions
* Internal documents
* Configuration information

This can help attackers during later reconnaissance.

---

# 19. SMB and Lateral Movement

In Windows/Active Directory environments, SMB can become relevant to lateral movement.

Conceptually:

```text
Compromised Host A
       |
       | authenticated network access
       ↓
Windows Host B
       |
       └── SMB
             |
             ↓
        Shared resources
```

A pentester may assess whether users have inappropriate access to other systems.

### Important

Lateral movement should only be tested inside an authorized scope.

---

# 20. SMB and Active Directory

SMB is particularly important in Windows domain environments.

You may encounter:

```text
Domain Controller
      |
      ├── SYSVOL
      ├── NETLOGON
      └── SMB
```

### SYSVOL

Stores domain-related files, including Group Policy data and scripts.

### NETLOGON

Used for domain logon-related resources and scripts.

These shares can be important during authorized Active Directory assessments.

---

# 21. SMB and Ransomware

SMB is also relevant to defensive security.

Ransomware can abuse network file shares to spread or encrypt accessible files.

Example defensive scenario:

```text
Infected PC
    |
    | SMB
    ↓
File Server
    |
    ├── Shared files
    ├── Backups
    └── Other systems
```

This is why organizations implement:

* Network segmentation
* Least privilege
* Strong authentication
* Monitoring
* Backup isolation
* SMB hardening

---

# 22. SMB Vulnerability Example: EternalBlue

One famous historical example is **EternalBlue**, associated with a vulnerability in older SMBv1 implementations.

The lesson is important:

```text
Legacy service
      +
Unpatched vulnerability
      +
Network exposure
      ↓
Large attack surface
```

### Key lesson

Don't memorize only the exploit name.

Understand the security chain:

```text
SMB exposure
→ vulnerable implementation
→ missing patch
→ exploitation possibility
→ potential compromise
```

---

# 23. SMB Security Hardening

A defender should consider:

### Disable SMB1 where unnecessary

Use modern SMB versions.

### Restrict network exposure

Do not unnecessarily expose SMB to untrusted networks.

### Use firewalls

Limit access to trusted hosts/networks.

### Apply patches

Keep Windows/Samba systems updated.

### Least privilege

Users should only access required shares.

### Strong authentication

Avoid weak authentication configurations.

### Monitor SMB

Look for unusual:

* Authentication attempts
* Share access
* File operations
* Connections between unusual hosts

---

# 24. Windows Practical Checks

On Windows, PowerShell can show SMB server configuration.

Example:

```powershell
Get-SmbServerConfiguration
```

List SMB shares:

```powershell
Get-SmbShare
```

View SMB connections:

```powershell
Get-SmbSession
```

These are useful for **defensive administration and lab analysis**.

---

# 25. Linux/Samba Practical Checks

On a Samba server, configuration is commonly associated with:

```text
/etc/samba/smb.conf
```

Check whether Samba is running:

```bash
systemctl status smbd
```

View listening sockets:

```bash
ss -lntp
```

You may see SMB-related ports such as:

```text
:445
:139
```

---

# 26. SMB Enumeration Workflow

A clean VAPT workflow:

```text
              SMB Assessment
                    |
                    ↓
             Port Discovery
                    |
                    ↓
              139 / 445?
                    |
                    ↓
            Service Detection
                    |
                    ↓
          SMB Version Detection
                    |
                    ↓
            Share Enumeration
                    |
                    ↓
       Authentication Requirements
                    |
                    ↓
          Permission Assessment
                    |
                    ↓
         Sensitive Data Exposure?
                    |
                    ↓
       Vulnerability / Configuration
               Assessment
                    |
                    ↓
             Risk Evaluation
                    |
                    ↓
                 Report
```

---

# 27. What Should You Record During an Assessment?

A professional report might contain:

| Item           | Example                        |
| -------------- | ------------------------------ |
| Host           | `10.10.10.20`                  |
| Port           | `445/TCP`                      |
| Service        | SMB                            |
| Version        | SMB2/SMB3                      |
| Shares         | Public, Documents              |
| Authentication | Required                       |
| Access         | Read-only                      |
| Sensitive Data | None                           |
| Finding        | Configuration issue            |
| Severity       | Based on impact/exploitability |

Don't report:

> "Port 445 is open = Critical"

That is incorrect.

An open port is an **attack surface**, not automatically a vulnerability.

---

# 28. SMB vs FTP

| Feature             | SMB                              | FTP                            |
| ------------------- | -------------------------------- | ------------------------------ |
| Common ecosystem    | Windows networks                 | General file transfer          |
| Typical port        | 445                              | 21                             |
| Authentication      | Windows/domain/local mechanisms  | FTP credentials                |
| File sharing        | Yes                              | Yes                            |
| Printer sharing     | Yes                              | No                             |
| Windows integration | Very strong                      | Limited                        |
| Encryption          | Depends on configuration/version | Traditional FTP is unencrypted |

---

# 29. SMB vs SFTP

Don't confuse **SMB** with **SFTP**.

### SMB

```text
Windows file/resource sharing
```

### SFTP

```text
Secure File Transfer Protocol
over SSH
```

SFTP commonly uses:

```text
TCP/22
```

SMB commonly uses:

```text
TCP/445
```

---

# 30. Important Terminology

| Term     | Meaning                                    |
| -------- | ------------------------------------------ |
| SMB      | Server Message Block protocol              |
| Samba    | SMB implementation for Unix/Linux          |
| Share    | Network-accessible SMB resource            |
| UNC      | `\\server\share` path format               |
| NTLM     | Windows authentication protocol            |
| Kerberos | Authentication protocol used heavily in AD |
| SMB1     | Legacy SMB version                         |
| SMB2/3   | Modern SMB generations                     |
| SYSVOL   | Domain-related shared directory            |
| NETLOGON | Domain logon-related share                 |
| 445      | Direct SMB over TCP                        |
| 139      | SMB over NetBIOS session service           |

---

# 31. Common Beginner Mistakes

### ❌ Mistake 1

> "445 open means system is hacked."

Wrong.

It means SMB is exposed/listening.

---

### ❌ Mistake 2

> "SMB and Samba are the same thing."

Wrong.

```text
SMB = protocol
Samba = implementation
```

---

### ❌ Mistake 3

> "SMB always uses NTLM."

Wrong.

Authentication depends on the environment/configuration.

---

### ❌ Mistake 4

> "Anonymous SMB access automatically means critical vulnerability."

Not necessarily.

You need to determine what information/resources are exposed and their impact.

---

### ❌ Mistake 5

Only looking for exploits.

A professional assessment also examines:

```text
Exposure
Configuration
Authentication
Authorization
Data
Patch level
Monitoring
Impact
```

---

# 32. Practical Lab — SMB Enumeration

Use a **local Windows/Samba lab** that you own or are explicitly authorized to test.

### Step 1 — Discover SMB

```bash
nmap -p 139,445 <LAB-IP>
```

### Step 2 — Identify SMB protocols

```bash
nmap -p445 --script smb-protocols <LAB-IP>
```

### Step 3 — Enumerate shares

```bash
smbclient -L //<LAB-IP>/ -N
```

### Step 4 — Analyze access

Ask:

```text
Can anonymous users connect?
Which shares exist?
Which shares require credentials?
What permissions are available?
Is sensitive information exposed?
```

### Step 5 — Document

Create:

```text
smb-assessment.md
```

Record:

```text
Target:
SMB Ports:
SMB Version:
Shares:
Authentication:
Permissions:
Sensitive Data:
Security Issues:
Recommendations:
```

This turns a command exercise into an actual VAPT workflow.

---

# 33. Scenario-Based Questions

### Scenario 1

You scan a lab machine and find:

```text
445/tcp open
```

What should you do next?

**Answer:** Perform SMB service/version and configuration enumeration.

---

### Scenario 2

A share is accessible anonymously and contains only:

```text
readme.txt
```

Is this automatically critical?

**Answer:** No. Determine the sensitivity and impact of the exposed information.

---

### Scenario 3

A company exposes SMB directly to the public Internet.

Why is this concerning?

**Answer:** SMB is a sensitive network service and unnecessary exposure increases attack surface and can expose authentication/services to untrusted networks.

---

### Scenario 4

A share allows a normal employee to modify sensitive application files.

What security principle may be violated?

**Answer:** Least privilege / improper authorization.

---

### Scenario 5

An old Windows server supports SMB1.

What should you investigate?

**Answer:** Whether SMB1 is actually required and whether it can be disabled; also assess patch/configuration status.

---

# 34. Interview Questions

### Q1. What is SMB?

A network protocol used primarily for file, printer, and resource sharing.

### Q2. What is the main SMB port?

**TCP 445.**

### Q3. What is port 139?

SMB communication associated with NetBIOS Session Service.

### Q4. What is Samba?

An implementation of SMB functionality for Unix/Linux systems.

### Q5. What is an SMB share?

A network-accessible resource exposed through SMB.

### Q6. What is a UNC path?

A path such as:

```text
\\server\share
```

### Q7. What is SMB1?

An old/legacy SMB protocol version.

### Q8. Why is SMB important in VAPT?

Because it can expose file shares, authentication services, sensitive information, and network attack surface.

### Q9. Authentication vs authorization?

```text
Authentication = Who are you?
Authorization = What can you access?
```

### Q10. What is SYSVOL?

A domain-related shared directory used for Group Policy and other domain information.

---

# 35. MCQs

### 1. SMB primarily provides:

A. DNS resolution
B. File/resource sharing
C. Email delivery
D. IP addressing

**Answer: B**

---

### 2. Which port is most commonly associated with modern SMB?

A. 21
B. 22
C. 80
D. 445

**Answer: D**

---

### 3. Samba is:

A. A firewall
B. An SMB implementation
C. A DNS protocol
D. An encryption algorithm

**Answer: B**

---

### 4. Which is an SMB UNC path?

A. `/etc/passwd`
B. `https://server/file`
C. `\\server\share`
D. `ftp://server`

**Answer: C**

---

### 5. SMB1 is generally considered:

A. Modernest SMB version
B. Legacy
C. DNS protocol
D. VPN protocol

**Answer: B**

---

### 6. What does authentication answer?

A. What files exist?
B. Who are you?
C. Which port is open?
D. What is the IP?

**Answer: B**

---

### 7. What does authorization determine?

A. Identity
B. Encryption algorithm
C. What resources a user can access
D. DNS server

**Answer: C**

---

### 8. Which tool can interact with SMB shares from Kali?

A. `smbclient`
B. `dig`
C. `curl` only
D. `traceroute`

**Answer: A**

---

### 9. Which Nmap script can help identify SMB protocol versions?

A. `http-title`
B. `smb-protocols`
C. `dns-brute`
D. `ftp-anon`

**Answer: B**

---

### 10. An open SMB port means:

A. The host is automatically compromised
B. A service/attack surface is exposed
C. The password is weak
D. Ransomware is present

**Answer: B**

---

### 11. Which share is commonly associated with Windows domain policy information?

A. SYSVOL
B. HTML
C. MAIL
D. DNS

**Answer: A**

---

### 12. Which is a good SMB hardening measure?

A. Expose SMB to the entire Internet
B. Enable unnecessary legacy protocols
C. Apply least privilege
D. Disable authentication

**Answer: C**

---

# 36. Must Remember 🔥

```text
SMB
│
├── Protocol → Server Message Block
│
├── Main port → TCP 445
│
├── Legacy → SMB1
│
├── Linux implementation → Samba
│
├── Shares → Files / printers / resources
│
├── UNC → \\server\share
│
├── Authentication → Identity
│
├── Authorization → Permissions
│
├── Windows/AD relevance → VERY HIGH
│
└── VAPT → Enumeration + Configuration + Access + Exposure
```

### Port Memory

```text
21   → FTP
22   → SSH / SFTP
25   → SMTP
53   → DNS
80   → HTTP
139  → NetBIOS Session / SMB
443  → HTTPS
445  → SMB
3389 → RDP
```

---

# 37. Key Takeaways

1. **SMB is a network resource-sharing protocol.**
2. **TCP 445 is the most important SMB port to remember.**
3. **139 is associated with SMB over NetBIOS.**
4. **Samba is an SMB implementation, not the protocol itself.**
5. SMB shares can contain highly sensitive information.
6. Authentication and authorization are different concepts.
7. SMB is especially important in Windows/Active Directory assessments.
8. Legacy SMB1 should generally be avoided when unnecessary.
9. An open SMB port is **not automatically a vulnerability**.
10. Professional VAPT focuses on **exposure + configuration + permissions + impact**, not simply finding open ports.

---

# 38. Final Mental Model 🧠

When you see:

```text
445/tcp open
```

Think:

```text
        445
         ↓
        SMB
         ↓
   What version?
         ↓
   What shares?
         ↓
 Authentication?
         ↓
   What access?
         ↓
Sensitive information?
         ↓
Configuration issue?
         ↓
Known vulnerability?
         ↓
       Impact
         ↓
      Report
```

