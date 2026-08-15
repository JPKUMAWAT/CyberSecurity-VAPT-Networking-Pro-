

# 1. What is SMTP?

**SMTP = Simple Mail Transfer Protocol**

SMTP is the protocol primarily used to **send and relay email**.

Simple example:

```text
Alice
  |
  | Send email
  ↓
Mail Client
  |
  | SMTP
  ↓
Alice's Mail Server
  |
  | SMTP
  ↓
Bob's Mail Server
  |
  ↓
Bob's Mailbox
```

### Important

SMTP is primarily about **sending/relaying mail**.

For retrieving mail, protocols such as **IMAP** and **POP3** are commonly used.

---

# 2. SMTP vs IMAP vs POP3

This distinction is extremely important.

| Protocol | Main Purpose               | Common Ports |
| -------- | -------------------------- | ------------ |
| SMTP     | Send/relay email           | 25, 587, 465 |
| POP3     | Download/retrieve email    | 110, 995     |
| IMAP     | Synchronize/access mailbox | 143, 993     |

### Easy Memory

```text
SMTP → Send
IMAP → Access + Synchronize
POP3 → Download
```

---

# 3. SMTP Ports

### TCP 25

Traditionally used for:

```text
Server ↔ Server
```

mail transfer.

### TCP 587

Commonly used for **mail submission** by authenticated clients.

### TCP 465

Commonly used for SMTP submission with TLS from the beginning of the connection.

### Must Remember

```text
25  → SMTP relay
587 → SMTP submission
465 → SMTP over TLS
```

Exact deployment can vary by mail infrastructure.

---

# 4. How Email Delivery Works

Suppose:

```text
alice@example.com
```

sends an email to:

```text
bob@example.net
```

Conceptually:

```text
Alice
  |
  ↓
Mail Client
  |
  | SMTP Submission
  ↓
example.com Mail Server
  |
  | DNS lookup for example.net
  ↓
MX Record
  |
  ↓
example.net Mail Server
  |
  ↓
Bob's Mailbox
```

The receiving mail server is determined using **DNS MX records**.

---

# 5. Role of DNS in Email

DNS is extremely important for email.

A domain can publish:

```text
MX
A / AAAA
TXT
```

records.

### MX Record

**MX = Mail Exchange**

It tells other mail systems which servers handle mail for a domain.

Example concept:

```text
example.com
     |
     ↓
MX
     |
     ↓
mail.example.com
```

---

# 6. Checking MX Records

In an authorized lab or for domains you are allowed to inspect:

```bash
dig MX example.com
```

Or:

```bash
nslookup -type=MX example.com
```

You might see:

```text
example.com
   MX
   ↓
mail.example.com
```

---

# 7. SMTP Communication

SMTP is a **text-based protocol**.

A simplified interaction looks like:

```text
Client → Server
EHLO client.example

Server → Client
250 ...

Client → Server
MAIL FROM:<alice@example.com>

Server → Client
250 OK

Client → Server
RCPT TO:<bob@example.net>

Server → Client
250 OK

Client → Server
DATA
```

The server then accepts the message contents.

Finally:

```text
QUIT
```

---

# 8. Important SMTP Commands

| Command     | Purpose                               |
| ----------- | ------------------------------------- |
| `HELO`      | Identify client using older SMTP flow |
| `EHLO`      | Extended SMTP greeting                |
| `MAIL FROM` | Specify sender                        |
| `RCPT TO`   | Specify recipient                     |
| `DATA`      | Begin message content                 |
| `RSET`      | Reset current transaction             |
| `NOOP`      | No operation                          |
| `QUIT`      | Close connection                      |

### Important

`EHLO` is generally preferred with **ESMTP**.

---

# 9. SMTP Response Codes

SMTP uses numeric response codes.

Common examples:

| Code | Meaning                         |
| ---: | ------------------------------- |
|  220 | Service ready                   |
|  221 | Service closing                 |
|  250 | Requested action successful     |
|  354 | Start mail input                |
|  421 | Service unavailable             |
|  450 | Mailbox unavailable temporarily |
|  451 | Temporary processing error      |
|  550 | Mailbox unavailable / rejected  |
|  554 | Transaction failed              |

### Easy Rule

```text
2xx → Success
4xx → Temporary failure
5xx → Permanent/rejection-type failure
```

---

# 10. SMTP and TLS

Traditional SMTP communication can be unencrypted.

That's why secure deployments use TLS.

Two important concepts:

### STARTTLS

The connection starts normally and then attempts to upgrade to TLS.

Conceptually:

```text
TCP
 ↓
SMTP
 ↓
STARTTLS
 ↓
TLS
 ↓
Encrypted SMTP
```

### Implicit TLS

TLS is established immediately when the connection begins.

Commonly associated with:

```text
TCP 465
```

---

# 11. Why Email Encryption Matters

Without appropriate transport encryption, network observers may potentially see sensitive email traffic.

Potentially exposed information can include:

* Sender
* Recipient
* Message contents
* Attachments
* Authentication information, depending on configuration

### Security principle

> Sensitive communication should use appropriate encryption in transit.

---

# 12. SMTP Authentication

Modern mail submission commonly uses authentication.

Conceptually:

```text
Client
  |
  ↓
SMTP Submission Server
  |
  ↓
Authentication
  |
  ├── Failed → Reject
  |
  └── Successful
         ↓
      Send mail
```

Authentication mechanisms vary by deployment.

---

# 13. Open Relay

One of the most important SMTP security concepts is an **open relay**.

An SMTP server is an open relay when it improperly allows unauthorized users to relay mail through it.

Conceptually:

```text
Attacker
   |
   ↓
SMTP Server
   |
   | Unauthorized relay
   ↓
Internet
```

This can allow attackers to abuse the server for spam or other malicious activity.

---

# 14. Why Open Relay is Dangerous

Possible consequences:

* Spam abuse
* Domain/IP reputation damage
* Blacklisting
* Phishing infrastructure abuse
* Increased operational costs

Therefore:

> SMTP servers should enforce appropriate relay restrictions.

---

# 15. SMTP Enumeration

During an authorized assessment, SMTP can reveal useful information.

Potential information includes:

* Server software
* Hostname
* Supported SMTP extensions
* TLS support
* Authentication mechanisms
* Relay behavior
* Error behavior

The goal is to understand the **attack surface**, not simply collect information.

---

# 16. Nmap SMTP Enumeration

For a system you are authorized to test:

```bash
nmap -p25,465,587 <LAB-IP>
```

Basic SMTP-related scripts can be discovered with:

```bash
nmap --script-help smtp-* 
```

For example, Nmap's SMTP capability scripts can provide additional service information.

Always verify what a script does before running it against a production target.

---

# 17. SMTP Banner

A mail server may expose a greeting/banner such as:

```text
220 mail.example.com ESMTP ...
```

This can potentially reveal:

* Hostname
* Mail software
* Version information

### Security concern

Excessive software/version disclosure can make reconnaissance easier.

However:

> Banner disclosure alone does not automatically mean a serious vulnerability exists.

---

# 18. Email Spoofing

Email spoofing means making an email appear to originate from an address that the sender does not legitimately control.

For example, the visible:

```text
From: ceo@example.com
```

doesn't necessarily prove that the message actually came from the CEO.

This is why modern email security uses authentication mechanisms such as:

* SPF
* DKIM
* DMARC

---

# 19. SPF

**SPF = Sender Policy Framework**

SPF allows a domain to publish which systems are authorized to send mail for that domain.

It is published using a DNS TXT record.

Conceptually:

```text
example.com
     |
     ↓
SPF TXT record
     |
     ↓
Authorized sending servers
```

---

# 20. DKIM

**DKIM = DomainKeys Identified Mail**

DKIM uses cryptographic signatures to help verify that a message is associated with a domain and has not been improperly altered in transit.

Conceptually:

```text
Sender
  |
  | Sign message
  ↓
Email
  |
  ↓
Recipient
  |
  | Verify signature
  ↓
Pass / Fail
```

A public DKIM key is published in DNS.

---

# 21. DMARC

**DMARC = Domain-based Message Authentication, Reporting & Conformance**

DMARC builds on SPF and DKIM.

It allows domain owners to specify how receiving systems should handle messages that fail authentication/alignment checks.

Common policy concepts:

```text
p=none
p=quarantine
p=reject
```

### Meaning

```text
none       → monitor
quarantine → treat suspiciously
reject     → reject failing messages
```

---

# 22. SPF + DKIM + DMARC

Think of them together:

```text
                 Email
                   |
          ┌────────┴────────┐
          ↓                 ↓
        SPF               DKIM
          |                 |
          └────────┬────────┘
                   ↓
                 DMARC
                   |
          Authentication/
             alignment
                   |
          ┌────────┴────────┐
          ↓                 ↓
        Accept           Reject/
                         Quarantine
```

### Easy Memory

```text
SPF  → Who can send?
DKIM → Is the message cryptographically signed?
DMARC → What should receivers do when authentication/alignment fails?
```

---

# 23. Email Header Security

Email headers contain valuable information.

Examples:

```text
From:
To:
Subject:
Date:
Message-ID:
Received:
Reply-To:
Authentication-Results:
```

### `Received`

Very important for analysis.

Multiple mail servers may add `Received` headers as the message travels.

Conceptually:

```text
Sender
 ↓
Mail Server A
 ↓
Mail Server B
 ↓
Recipient
```

The headers can help investigators reconstruct the mail path.

---

# 24. Authentication-Results

A receiving mail server may add an:

```text
Authentication-Results:
```

header.

It can contain results related to mechanisms such as:

```text
SPF
DKIM
DMARC
```

This is useful during email security analysis.

---

# 25. Email Security VAPT Workflow

A professional authorized assessment might follow:

```text
          Email Security Assessment
                    |
                    ↓
              DNS Discovery
                    |
             ┌──────┴──────┐
             ↓             ↓
            MX            TXT
             |             |
             ↓             ↓
         Mail Servers   SPF/DKIM/DMARC
             |
             ↓
       SMTP Port Review
             |
             ↓
       Service Enumeration
             |
             ↓
      TLS Configuration
             |
             ↓
      Authentication Review
             |
             ↓
        Relay Controls
             |
             ↓
       Security Headers
             |
             ↓
        Risk Assessment
             |
             ↓
            Report
```

---

# 26. What to Check During VAPT

| Area           | Question                                                  |
| -------------- | --------------------------------------------------------- |
| SMTP exposure  | Which SMTP services are reachable?                        |
| TLS            | Is transport encryption supported/enforced appropriately? |
| Authentication | Is authentication required where appropriate?             |
| Relay          | Can unauthorized users relay mail?                        |
| Banner         | Is excessive version information exposed?                 |
| SPF            | Is SPF configured correctly?                              |
| DKIM           | Is DKIM configured?                                       |
| DMARC          | Is DMARC configured with an appropriate policy?           |
| Headers        | Do headers leak unnecessary information?                  |
| Authentication | Are weak/legacy mechanisms unnecessarily enabled?         |

---

# 27. Common SMTP Security Issues

### 1. Open Relay

Unauthorized mail relay.

### 2. Weak TLS Configuration

Poor or outdated encryption configuration.

### 3. Missing SPF

Makes domain spoofing protection weaker.

### 4. Missing DKIM

Removes an important message-signing mechanism.

### 5. Weak DMARC Policy

For example:

```text
p=none
```

may provide monitoring but does not instruct receivers to reject failing messages.

### 6. Excessive Banner Disclosure

Revealing unnecessary software/version information.

### 7. Weak Authentication

Poorly configured authentication can expose accounts.

---

# 28. SMTP and Phishing

Email infrastructure is heavily involved in phishing attacks.

Typical chain:

```text
Fake/Compromised Sender
        |
        ↓
Email Infrastructure
        |
        ↓
Victim
        |
        ↓
Malicious Link / Attachment
```

Defenses include:

* SPF
* DKIM
* DMARC
* Secure email gateways
* URL scanning
* Attachment analysis
* User awareness
* MFA

---

# 29. Email Security Is More Than SMTP

Remember:

```text
Email Security
      |
      ├── SMTP
      ├── IMAP
      ├── POP3
      ├── DNS
      ├── SPF
      ├── DKIM
      ├── DMARC
      ├── TLS
      ├── Authentication
      └── Endpoint Security
```

SMTP is only one component.

---

# 30. Practical Lab — Discover Email Infrastructure

Use a domain you own or are explicitly authorized to assess.

### Step 1 — Find MX

```bash
dig MX example.com
```

### Step 2 — Find TXT records

```bash
dig TXT example.com
```

Look for:

```text
SPF
DKIM-related records
DMARC-related information
```

### Step 3 — Find DMARC

```bash
dig TXT _dmarc.example.com
```

You may see something like:

```text
v=DMARC1; p=...
```

### Step 4 — Identify SMTP ports on your lab server

```bash
nmap -p25,465,587 <LAB-IP>
```

### Step 5 — Document

Create:

```text
smtp-assessment.md
```

Record:

```text
Domain:
MX:
SMTP Ports:
TLS:
Authentication:
SPF:
DKIM:
DMARC:
Relay Controls:
Potential Issues:
Recommendations:
```

---

# 31. Defensive Linux Lab

If you're running your own Linux mail server, first check the service.

For example:

```bash
ss -lntp
```

You can look for:

```text
:25
:465
:587
```

Then review the mail server's configuration according to the specific software documentation.

**Do not blindly change production mail-server configuration.**

---

# 32. Windows Perspective

Windows administrators may encounter SMTP through:

* Microsoft Exchange
* Third-party mail servers
* Cloud email services

In enterprise environments, email security also involves:

```text
Identity
Active Directory
MFA
Conditional Access
Mail filtering
Endpoint protection
DNS
```

So SMTP knowledge becomes especially valuable when combined with **Windows/AD security**.

---

# 33. Common Beginner Mistakes

### ❌ Mistake 1

> SMTP is used to receive email.

Not exactly.

SMTP primarily handles sending/relaying.

---

### ❌ Mistake 2

> Port 25 = the only SMTP port.

Wrong.

Common SMTP-related ports include:

```text
25
465
587
```

---

### ❌ Mistake 3

> SPF encrypts email.

Wrong.

SPF is an email sender authorization mechanism published through DNS.

---

### ❌ Mistake 4

> DKIM encrypts email.

Wrong.

DKIM provides a cryptographic signature mechanism; it is not general message encryption.

---

### ❌ Mistake 5

> DMARC sends email.

Wrong.

DMARC defines policy and reporting around email authentication/alignment.

---

### ❌ Mistake 6

> Open port = vulnerability.

Wrong.

An exposed SMTP service is an **attack surface**. You must assess its configuration and behavior.

---

# 34. Interview Questions

### Q1. What is SMTP?

A protocol primarily used for sending and relaying email.

### Q2. What is SMTP port 25?

Traditionally used for server-to-server SMTP relay.

### Q3. What is port 587?

Commonly used for authenticated mail submission.

### Q4. What is port 465?

Commonly used for SMTP submission with implicit TLS.

### Q5. What is an MX record?

A DNS record identifying mail servers responsible for receiving mail for a domain.

### Q6. What is SPF?

A DNS-based mechanism that specifies authorized sending hosts for a domain.

### Q7. What is DKIM?

A cryptographic email-signing mechanism associated with a domain.

### Q8. What is DMARC?

A policy/reporting framework built around SPF/DKIM authentication and alignment.

### Q9. What is an open relay?

An SMTP server that improperly allows unauthorized mail relay.

### Q10. Why is SMTP important in VAPT?

Because mail infrastructure can expose authentication, relay, TLS, configuration, and domain-security weaknesses.

---

# 35. Scenario-Based Questions

### Scenario 1

You find:

```text
TCP/25 open
```

What does this tell you?

**Answer:** An SMTP service may be exposed. Perform authorized service/configuration enumeration.

---

### Scenario 2

A domain has:

```text
SPF → configured
DKIM → configured
DMARC → p=none
```

Is the email security automatically perfect?

**Answer:** No. `p=none` is primarily a monitoring policy; the complete configuration and alignment/results must be assessed.

---

### Scenario 3

An SMTP server allows arbitrary external senders to relay mail to arbitrary external recipients.

What is the likely issue?

**Answer:** Open relay.

---

### Scenario 4

An SMTP service supports weak or outdated TLS settings.

What security property is affected?

**Answer:** Confidentiality/integrity of communication in transit.

---

### Scenario 5

A mail server reveals its exact software and version in its banner.

Is that automatically critical?

**Answer:** No. It is generally information disclosure; severity depends on context and whether the disclosed version is vulnerable.

---

# 36. MCQs

### 1. SMTP primarily handles:

A. File transfer
B. Email sending/relay
C. DNS resolution
D. Remote desktop

**Answer: B**

### 2. Which port is commonly used for SMTP submission?

A. 22
B. 53
C. 587
D. 3389

**Answer: C**

### 3. Which DNS record identifies mail servers?

A. A
B. MX
C. PTR
D. CNAME

**Answer: B**

### 4. SPF is mainly used to:

A. Encrypt email
B. Identify authorized sending systems
C. Store mailboxes
D. Transfer attachments

**Answer: B**

### 5. DKIM uses:

A. Cryptographic signatures
B. DHCP
C. FTP
D. NAT

**Answer: A**

### 6. DMARC primarily provides:

A. File encryption
B. Email authentication policy/reporting
C. DNS resolution
D. TCP routing

**Answer: B**

### 7. An open relay can be abused for:

A. DNS caching
B. Spam relay
C. IP addressing
D. DHCP assignment

**Answer: B**

### 8. Which protocol is primarily used to retrieve/synchronize email?

A. SMTP
B. IMAP
C. ARP
D. SSH

**Answer: B**

### 9. Which port is traditionally associated with SMTP server-to-server communication?

A. 25
B. 80
C. 110
D. 445

**Answer: A**

### 10. Which command can query MX records?

A. `dig MX example.com`
B. `ping MX example.com`
C. `ssh MX example.com`
D. `chmod MX example.com`

**Answer: A**

### 11. What does STARTTLS generally do?

A. Deletes email
B. Attempts to upgrade a connection to TLS
C. Changes DNS
D. Creates an MX record

**Answer: B**

### 12. Which protocol is normally associated with mailbox retrieval rather than sending?

A. SMTP
B. IMAP
C. TCP
D. DNS

**Answer: B**

### 13. Which issue describes unauthorized SMTP relay?

A. Open redirect
B. Open relay
C. DNS poisoning
D. Session fixation

**Answer: B**

### 14. Which header can help trace mail-server hops?

A. `Received`
B. `Content-Length`
C. `Host`
D. `Cookie`

**Answer: A**

### 15. Which statement is correct?

A. SPF encrypts email
B. DKIM is a file-transfer protocol
C. DMARC works with SPF/DKIM concepts
D. SMTP is DNS

**Answer: C**

---

# 37. Must-Memorize Cheat Sheet 🔥

```text
SMTP
│
├── Purpose → Send / Relay Email
│
├── 25  → Traditional SMTP relay
├── 587 → Mail submission
├── 465 → SMTP with implicit TLS
│
├── MX → Mail server location
│
├── SPF → Authorized senders
├── DKIM → Cryptographic signature
├── DMARC → Authentication policy/reporting
│
├── Open Relay → Unauthorized mail relay
│
└── VAPT
    ├── Enumeration
    ├── TLS
    ├── Authentication
    ├── Relay controls
    ├── SPF
    ├── DKIM
    └── DMARC
```

### One-Line Memory

> **SMTP sends, MX finds mail servers, SPF identifies authorized senders, DKIM signs, and DMARC defines how authentication failures should be handled.**

---

# 38. Key Takeaways

* SMTP is primarily an **email sending/relay protocol**.
* **TCP 25** is traditionally used for server-to-server SMTP.
* **587** is commonly used for authenticated submission.
* **465** commonly uses implicit TLS.
* **MX records** identify mail-handling servers.
* **SPF, DKIM and DMARC** are major domain-level email security mechanisms.
* An **open relay** is a serious configuration problem.
* TLS protects communication in transit when correctly configured.
* Email headers can provide valuable forensic information.
* SMTP security should be assessed as part of the larger **email + DNS + identity + endpoint security architecture**.

---

# 39. Final Mental Model 🧠

When you discover:

```text
25/tcp open
```

don't immediately think:

> "Exploit SMTP."

Think:

```text
SMTP detected
     ↓
Who is the mail server?
     ↓
What software/configuration?
     ↓
TLS supported?
     ↓
Authentication?
     ↓
Relay restricted?
     ↓
SPF configured?
     ↓
DKIM configured?
     ↓
DMARC configured?
     ↓
Any sensitive information exposed?
     ↓
What is the actual business impact?
     ↓
Document + Report
```
