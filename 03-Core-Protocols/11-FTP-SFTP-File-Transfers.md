

# 1. What Are FTP and SFTP?

Both are protocols used to **transfer files between systems**, but their security models are very different.

| Protocol | Full Form                  | Transport | Encryption                 | Default Port |
| -------- | -------------------------- | --------- | -------------------------- | -----------: |
| FTP      | File Transfer Protocol     | TCP       | ❌ No encryption by default |           21 |
| FTPS     | FTP Secure                 | TCP       | ✅ TLS                      |     21 / 990 |
| SFTP     | SSH File Transfer Protocol | TCP       | ✅ SSH encryption           |           22 |

### Important

**SFTP is NOT "Secure FTP".**

SFTP is a separate file-transfer protocol that operates over **SSH**.

---

# 2. FTP — File Transfer Protocol

FTP is an application-layer protocol designed for transferring files.

Basic architecture:

```text
FTP Client                         FTP Server
    |                                  |
    |------ Control Connection ------->|
    |              TCP 21              |
    |                                  |
    |<--------- File Transfer -------->|
    |                                  |
```

FTP traditionally uses **two connections**:

1. Control connection
2. Data connection

---

# 3. FTP Control Connection

The control connection is normally established to:

```text
TCP/21
```

It is used for commands such as:

```text
USER
PASS
LIST
RETR
STOR
QUIT
```

Example:

```text
Client → Server
USER alice

Server → Client
331 Username okay, need password.

Client → Server
PASS ********

Server → Client
230 Login successful.
```

The actual file transfer is handled separately.

---

# 4. FTP Data Connection

FTP has two major transfer modes:

```text
Active FTP
Passive FTP
```

Understanding this is important for firewall/NAT troubleshooting and VAPT.

---

# 5. Active FTP

In active mode:

```text
Client                         Server
  |                              |
  |---- TCP 21 ----------------->|
  |                              |
  |<--- Data connection ---------|
  |                              |
```

The server initiates the data connection back toward the client.

### Security/Network Problem

If the client is behind:

* NAT
* Firewall
* restrictive network

the incoming connection may be blocked.

---

# 6. Passive FTP

Passive mode is commonly easier to use through modern firewalls/NAT.

```text
Client                         Server
  |                              |
  |---- Control TCP/21 --------->|
  |                              |
  |---- Data connection -------->|
  |                              |
```

The server tells the client which port should be used for the data connection.

Example:

```text
Server:
Entering Passive Mode
Port: 50000
```

The client then connects to that server port.

### VAPT relevance

During an assessment, exposed passive FTP port ranges can be important when reviewing:

* Firewall rules
* Network segmentation
* Attack surface
* Unexpected exposed services

---

# 7. Common FTP Commands

Once connected to an FTP server, common commands include:

| Command | Purpose                |
| ------- | ---------------------- |
| `USER`  | Specify username       |
| `PASS`  | Authenticate           |
| `LIST`  | List files             |
| `PWD`   | Show current directory |
| `CWD`   | Change directory       |
| `RETR`  | Download file          |
| `STOR`  | Upload file            |
| `DELE`  | Delete file            |
| `QUIT`  | Close session          |

---

# 8. FTP Authentication

FTP may support:

### Anonymous FTP

Some servers allow:

```text
Username: anonymous
```

This can be intentional—for example, a public download server.

But unnecessary anonymous access can create a security issue.

### VAPT Question

> If anonymous FTP is enabled, is that automatically a vulnerability?

**No.**

You must determine:

* Is anonymous access intended?
* What files are accessible?
* Is upload allowed?
* Can sensitive information be accessed?
* Is the service exposed to the Internet?

---

# 9. Why Traditional FTP Is Insecure

Traditional FTP does **not encrypt the connection by default**.

That can expose:

```text
Username
Password
Commands
File contents
```

Conceptually:

```text
Client
  |
  |  USER alice
  |  PASS password
  |
Network
  |
  v
Server
```

Without encryption, someone able to observe the traffic may be able to obtain sensitive information.

### Security principle

> Authentication without encryption does not provide confidentiality.

---

# 10. SFTP

SFTP stands for:

# SSH File Transfer Protocol

It works through SSH.

Normally:

```text
TCP/22
```

Architecture:

```text
SFTP Client
     |
     | Encrypted SSH connection
     |
     v
SSH Server
     |
     +---- File subsystem
```

SFTP provides encrypted:

* Authentication
* Commands
* File transfers

---

# 11. FTP vs SFTP

| Feature                   | FTP             | SFTP            |
| ------------------------- | --------------- | --------------- |
| Encryption                | ❌               | ✅               |
| Based on                  | FTP             | SSH             |
| Default port              | 21              | 22              |
| Authentication protection | Weak by default | Stronger        |
| File transfer encryption  | ❌               | ✅               |
| Firewall complexity       | Higher          | Usually simpler |
| Common modern choice      | Less preferred  | Preferred       |

---

# 12. SFTP Authentication

SFTP can use:

### Password authentication

```text
Username + Password
```

### SSH key authentication

```text
Private Key
     |
     v
Client
     |
 Encrypted SSH
     |
     v
Server
```

SSH keys are commonly preferred for automated secure access.

---

# 13. Connecting to an SFTP Server

On Linux/Kali:

```bash
sftp username@192.168.1.10
```

You may see:

```text
Connected to 192.168.1.10.
sftp>
```

Common SFTP commands:

```text
ls
pwd
cd
get
put
mkdir
rm
exit
```

Example:

```bash
sftp user@192.168.1.10
```

Then:

```text
sftp> ls
sftp> pwd
sftp> get report.txt
sftp> exit
```

Only perform this against systems you own or are explicitly authorized to test.

---

# 14. FTP Client on Kali

A basic FTP client may be available as:

```bash
ftp <authorized-target>
```

Example lab:

```bash
ftp 192.168.56.101
```

You can also use:

```bash
curl ftp://192.168.56.101/
```

For authorized labs, this helps you understand how clients communicate with FTP servers.

---

# 15. Identifying FTP/SFTP During Recon

Nmap can identify services in an authorized lab:

```bash
nmap -sV -p 21,22 192.168.56.101
```

Possible output:

```text
21/tcp open  ftp
22/tcp open  ssh
```

### Interpretation

```text
21 → FTP
22 → SSH/SFTP
```

Remember:

> Port numbers are conventions, not proof of the application.

A service can run on a non-standard port.

---

# 16. FTP Enumeration in VAPT

When FTP is discovered, think systematically.

```text
FTP discovered
      |
      v
Is anonymous access enabled?
      |
      v
What files/directories are exposed?
      |
      v
Is upload allowed?
      |
      v
Are sensitive files accessible?
      |
      v
Is encryption being used?
      |
      v
Is the service unnecessarily Internet-facing?
```

---

# 17. Security Checks

During an authorized assessment, examine:

### 1. Anonymous access

```text
anonymous → allowed?
```

### 2. Read permissions

Can users download files they shouldn't?

### 3. Write permissions

Can users upload files?

### 4. Delete permissions

Can users delete files?

### 5. Directory traversal

Can users access files outside their intended directory?

### 6. Cleartext authentication

Is traditional FTP transmitting credentials without encryption?

### 7. Weak authentication

Are weak/default credentials being accepted?

Only test credentials according to the engagement scope.

---

# 18. FTP Attack Surface

FTP can expose several security risks:

```text
                    FTP
                     |
        +------------+-------------+
        |            |             |
   Anonymous     Weak Auth      Cleartext
     Access                     Credentials
        |            |             |
        v            v             v
   Data Exposure  Account Risk  Credential
                                Exposure
```

---

# 19. Anonymous FTP — VAPT Scenario

Imagine an assessment finds:

```text
21/tcp open ftp
```

You connect and discover:

```text
anonymous login successful
```

Then:

```text
reports/
backup/
internal/
```

The important question isn't simply:

> "Anonymous login works."

Instead ask:

> **"What security impact does anonymous access create?"**

For example:

```text
Anonymous access
       ↓
Sensitive backup accessible
       ↓
Information disclosure
       ↓
Potential credential exposure
       ↓
Higher security impact
```

---

# 20. FTP Upload Risk

Suppose anonymous users can upload files:

```text
anonymous
    |
    v
Upload permission
    |
    v
Untrusted files placed on server
```

This can become significantly more serious depending on:

* Where files are stored
* Whether they are publicly accessible
* Whether uploaded content is executed
* Server configuration
* Application integration

Never assume "upload = code execution." The surrounding configuration determines the actual impact.

---

# 21. FTP and Web Server Interaction

A particularly important architecture is:

```text
Internet
    |
    v
FTP Server
    |
    | uploaded files
    v
Web directory
    |
    v
Web Server
```

If a poorly configured FTP service allows untrusted users to write into a web-served directory, the security impact can become much greater.

This is a **configuration-dependent chain**, not an automatic property of FTP.

---

# 22. FTP vs FTPS vs SFTP

This is a common interview question.

|                      | FTP | FTPS      | SFTP  |
| -------------------- | --- | --------- | ----- |
| Encryption           | ❌   | ✅ TLS     | ✅ SSH |
| Protocol family      | FTP | FTP + TLS | SSH   |
| Typical control port | 21  | 21        | 22    |
| Uses SSH?            | ❌   | ❌         | ✅     |
| Uses TLS?            | ❌   | ✅         | ❌     |
| File transfer        | Yes | Yes       | Yes   |

### Memory trick

```text
FTP  → Plain FTP
FTPS → FTP + TLS
SFTP → SSH + File Transfer
```

---

# 23. FTP vs SFTP — Mental Model

Think:

```text
FTP

Client ──────── Plain communication ──────── Server
                    ❌


SFTP

Client ═══════ Encrypted SSH tunnel ═══════ Server
                    🔐
```

---

# 24. Python + SFTP

Python can automate SFTP operations using libraries such as Paramiko.

Conceptually:

```text
Python Script
     |
     v
Paramiko
     |
     v
SSH/SFTP
     |
     v
Remote Server
```

Typical legitimate automation:

* Upload reports
* Download logs
* Transfer assessment files
* Automate backups
* Retrieve authorized test results

---

# 25. Python Example — SFTP Automation

For an authorized lab:

```python
import paramiko

host = "192.168.56.101"
port = 22
username = "labuser"

client = paramiko.SSHClient()
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())

client.connect(host, port=port, username=username)

sftp = client.open_sftp()

sftp.get("/home/labuser/report.txt", "report.txt")

sftp.close()
client.close()

print("[+] File downloaded")
```

### Important

For real environments, don't blindly trust unknown host keys. Proper SSH host-key verification is an important security control.

---

# 26. Better SFTP Authentication

Instead of passwords, SSH keys can be used.

Conceptually:

```text
Client
 |
 | Private key
 |
 v
SSH Authentication
 |
 v
Server
 |
 | Public key verification
 v
Access granted
```

Never share your private SSH key.

---

# 27. Common FTP/SFTP Ports

| Port | Service                         |
| ---: | ------------------------------- |
|   20 | Traditional FTP data connection |
|   21 | FTP control                     |
|   22 | SSH / SFTP                      |
|  990 | Common FTPS implicit TLS        |

### Important

Port 20 is associated with **traditional active FTP data connections**, while passive FTP uses dynamically negotiated server-side ports.

---

# 28. Practical Lab — Build Your Own FTP Environment

A safe learning architecture:

```text
Kali VM
   |
   | Host-only network
   |
   v
Linux Server VM
   |
   +--- FTP
   |
   +--- SFTP
```

Use an isolated lab network rather than testing random Internet systems.

---

# 29. Lab Objectives

### Step 1 — Discover services

```bash
nmap -sV -p 21,22 <LAB-IP>
```

### Step 2 — Connect to FTP

```bash
ftp <LAB-IP>
```

### Step 3 — Test authorized credentials

```text
Username
Password
```

### Step 4 — Explore permissions

```text
LIST
PWD
CWD
```

### Step 5 — Test SFTP

```bash
sftp user@<LAB-IP>
```

### Step 6 — Transfer a harmless test file

```text
put test.txt
get test.txt
```

### Step 7 — Document findings

Record:

```text
Service
Port
Authentication
Encryption
Permissions
Accessible files
Security impact
Recommendation
```

---

# 30. Example VAPT Finding

## Finding

**FTP Service Allows Anonymous Access**

### Description

The FTP service permits unauthenticated/anonymous access, allowing users to access files without normal authentication.

### Risk

Potential unauthorized information disclosure depending on accessible content.

### Evidence

```text
21/tcp open ftp
anonymous login successful
```

### Impact

An attacker may potentially access:

* Internal documents
* Backups
* Configuration files
* Other sensitive information

### Recommendation

* Disable anonymous access unless explicitly required.
* Restrict accessible directories.
* Apply least-privilege permissions.
* Prefer encrypted file-transfer mechanisms such as SFTP or properly configured FTPS.
* Restrict network exposure with firewall rules.

---

# 31. Common Beginner Mistakes

### ❌ Mistake 1

Thinking:

```text
SFTP = FTP + encryption
```

### Correct

SFTP is an **SSH-based file-transfer protocol**.

---

### ❌ Mistake 2

Thinking:

```text
Port 21 = always FTP
```

Ports are conventions.

Always verify the actual service.

---

### ❌ Mistake 3

Thinking:

```text
Anonymous FTP = automatically critical
```

Severity depends on:

* Accessible information
* Permissions
* Exposure
* Business context
* Actual impact

---

### ❌ Mistake 4

Confusing FTPS and SFTP.

```text
FTPS → TLS
SFTP → SSH
```

---

### ❌ Mistake 5

Only checking whether FTP is open.

A professional assessment goes further:

```text
Open port
 ↓
Identify service
 ↓
Authentication
 ↓
Authorization
 ↓
Encryption
 ↓
Accessible data
 ↓
Permissions
 ↓
Impact
```

---

# 32. VAPT Methodology

When you encounter FTP:

```text
             FTP Found
                 |
                 v
        Service Identification
                 |
                 v
       Version / Configuration
                 |
                 v
        Authentication Testing
                 |
        +--------+--------+
        |                 |
   Anonymous?          Authenticated?
        |                 |
        v                 v
   Permissions       Access Control
        |                 |
        +--------+--------+
                 |
                 v
          Data Exposure
                 |
                 v
          Security Impact
                 |
                 v
             Report
```

---

# 33. Interview Questions

### Q1. What is FTP?

A protocol used for transferring files between systems over a network.

### Q2. What is the default FTP control port?

**TCP 21.**

### Q3. What is SFTP?

SSH File Transfer Protocol.

### Q4. What port does SFTP normally use?

**TCP 22.**

### Q5. Is SFTP based on FTP?

**No.** It operates through SSH.

### Q6. What is FTPS?

FTP secured using TLS.

### Q7. Why is traditional FTP insecure?

It does not provide encryption by default.

### Q8. What is anonymous FTP?

FTP access that allows login without a normal user account, commonly using an anonymous identity.

### Q9. What is passive FTP?

A mode where the client initiates both the control and data connections.

### Q10. Why is FTP enumeration useful?

It can reveal services, versions, authentication behavior, exposed files and permissions.

---

# 34. Scenario-Based Questions

### Scenario 1

You discover:

```text
21/tcp open ftp
```

What should you do first?

**Answer:** Identify the FTP implementation/version and examine its configuration and authentication behavior within scope.

---

### Scenario 2

Anonymous FTP gives access to:

```text
public/
```

Only harmless public documents are available.

Is this automatically a critical vulnerability?

**Answer:** No. Determine whether anonymous access is intended and whether the exposed data creates meaningful security impact.

---

### Scenario 3

SFTP is running on port 2222.

Is it still SFTP?

**Answer:** It can be. Port numbers don't determine the protocol by themselves.

---

### Scenario 4

FTP credentials are transmitted without encryption.

What is the primary security concern?

**Answer:** Credential and data confidentiality can be compromised if the traffic is observed.

---

# 35. MCQs

### 1. Default FTP control port?

A. 20
B. 21
C. 22
D. 23

**Answer: B**

---

### 2. SFTP normally operates over:

A. HTTP
B. FTP
C. SSH
D. DNS

**Answer: C**

---

### 3. FTPS uses:

A. TLS
B. SSH
C. DNSSEC
D. ICMP

**Answer: A**

---

### 4. Which protocol is encrypted by design through SSH?

A. FTP
B. SFTP
C. Telnet
D. TFTP

**Answer: B**

---

### 5. Traditional FTP normally uses:

A. Only one connection
B. Separate control/data connections
C. UDP only
D. ICMP

**Answer: B**

---

### 6. Which command downloads a file in SFTP?

A. `put`
B. `get`
C. `send`
D. `download`

**Answer: B**

---

### 7. Which command uploads a file in SFTP?

A. `put`
B. `get`
C. `pushfile`
D. `upload`

**Answer: A**

---

### 8. Port 22 is commonly associated with:

A. FTP
B. SSH/SFTP
C. DNS
D. SMTP

**Answer: B**

---

### 9. Anonymous FTP should be evaluated based primarily on:

A. Port number
B. Actual access and security impact
C. Server color
D. Operating system name

**Answer: B**

---

### 10. Which is generally the preferred secure file-transfer protocol between FTP and SFTP?

A. FTP
B. SFTP
C. Telnet
D. HTTP

**Answer: B**

---

# 36. Must Remember 🔥

```text
FTP  → File Transfer Protocol
FTPS → FTP + TLS
SFTP → SSH File Transfer Protocol
```

```text
FTP Control → TCP 21
FTP Active Data → TCP 20 (traditional)
SFTP → TCP 22
FTPS → commonly 21 or 990 depending on mode
```

### Security

```text
FTP
↓
No encryption by default
↓
Credential/data exposure risk
```

```text
SFTP
↓
SSH
↓
Encrypted communication
```

---

# 37. Quick Cheat Sheet

| Concept                     | Remember                                  |
| --------------------------- | ----------------------------------------- |
| FTP                         | File transfer protocol                    |
| FTP control                 | TCP 21                                    |
| Traditional active FTP data | TCP 20                                    |
| SFTP                        | SSH File Transfer Protocol                |
| SFTP port                   | TCP 22                                    |
| FTPS                        | FTP + TLS                                 |
| `LIST`                      | List files                                |
| `PWD`                       | Current directory                         |
| `CWD`                       | Change directory                          |
| `RETR`                      | Retrieve/download                         |
| `STOR`                      | Store/upload                              |
| `get`                       | SFTP download                             |
| `put`                       | SFTP upload                               |
| Anonymous FTP               | Unauthenticated/anonymous identity access |
| Main FTP weakness           | No encryption by default                  |

---

# 38. Final Mental Model

Don't memorize FTP as just:

> **Port 21 = FTP**

Think like a pentester:

```text
             File Transfer Service
                      |
          +-----------+-----------+
          |                       |
         FTP                     SFTP
          |                       |
       TCP/21                   SSH/22
          |                       |
     Not encrypted          Encrypted
       by default               |
          |                       |
          v                       v
   Check authentication     Check SSH/SFTP
   Check permissions        authentication
   Check exposed files      Check access control
   Check encryption         Check configuration
          |                       |
          +-----------+-----------+
                      |
                      v
                 Assess Impact
                      |
                      v
                    Report
```

## Key Takeaways

1. **FTP and SFTP are different protocols.**
2. **FTP normally uses TCP/21 for control.**
3. **Traditional FTP does not encrypt traffic by default.**
4. **SFTP works through SSH and normally uses TCP/22.**
5. **FTPS uses TLS; SFTP uses SSH.**
6. **Anonymous FTP is not automatically a critical vulnerability—the impact matters.**
7. In VAPT, investigate **authentication → authorization → encryption → exposed data → permissions → impact**.
8. Port numbers help with reconnaissance, but **service identification should be verified rather than assumed**.

