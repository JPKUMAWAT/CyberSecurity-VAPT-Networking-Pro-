

# 1. What Are WebSockets?

**WebSocket** is a network communication protocol that provides a **persistent, two-way communication channel** between a client and server.

Unlike normal HTTP, where communication usually follows:

```text
Client → Request
Server → Response
Connection/interaction ends
```

WebSockets allow:

```text
Client ⇄ Server
       ⇅
   continuous
 communication
```

Both sides can send messages whenever needed.

### Simple Example

A live chat application:

```text
Browser
   │
   │ WebSocket connection
   ▼
WebSocket Server
   │
   ├── User A sends message
   ├── Server broadcasts message
   └── User B receives it instantly
```

---

# 2. HTTP vs WebSocket

| Feature                   | HTTP                                   | WebSocket                        |
| ------------------------- | -------------------------------------- | -------------------------------- |
| Communication             | Request → Response                     | Two-way                          |
| Connection                | Usually short-lived/request based      | Persistent                       |
| Server can initiate data? | Not normally without another mechanism | Yes                              |
| Real-time communication   | Less suitable                          | Excellent                        |
| Typical usage             | Websites, APIs                         | Chat, gaming, live notifications |
| Protocol                  | HTTP/HTTPS                             | WS/WSS                           |
| Browser support           | Yes                                    | Yes                              |

### Easy Mental Model

HTTP:

> "I ask → you answer."

WebSocket:

> "We stay connected → either side can talk."

---

# 3. Where WebSockets Are Used

Common applications include:

* 💬 Chat applications
* 🎮 Multiplayer games
* 📈 Live dashboards
* 💰 Real-time trading interfaces
* 🔔 Notifications
* 📺 Live updates
* Collaborative editing
* IoT applications
* Real-time monitoring

---

# 4. WebSocket URLs

Two common schemes exist:

```text
ws://
```

and

```text
wss://
```

### `ws://`

WebSocket without TLS encryption.

Similar idea to:

```text
http://
```

### `wss://`

WebSocket over TLS.

Similar idea to:

```text
https://
```

For example:

```text
ws://example.com/chat
```

```text
wss://example.com/chat
```

### Security Recommendation

Production applications should normally use:

```text
WSS
```

because sensitive WebSocket traffic should be protected with TLS.

---

# 5. How a WebSocket Connection Starts

A very important concept:

**WebSocket communication begins with an HTTP-based handshake.**

Simplified flow:

```text
Client
  │
  │ HTTP Upgrade Request
  ▼
Server
  │
  │ 101 Switching Protocols
  ▼
WebSocket Connection
  │
  ⇅
Messages
```

---

# 6. WebSocket Handshake

The client initially sends an HTTP request containing an upgrade request.

Simplified example:

```http
GET /chat HTTP/1.1
Host: example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: <random-value>
Sec-WebSocket-Version: 13
```

Important headers include:

```text
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key
Sec-WebSocket-Version
```

---

# 7. Server Response

If the server accepts the upgrade, it responds approximately:

```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: <value>
```

The important status code is:

# `101 Switching Protocols`

It means the server is switching protocols from HTTP to WebSocket.

---

# 8. After the Handshake

After successful negotiation:

```text
HTTP
  │
  │ Upgrade
  ▼
WebSocket
  │
  ├── Client → Message
  ├── Server → Message
  ├── Server → Message
  ├── Client → Message
  └── ...
```

The communication becomes persistent.

---

# 9. WebSocket Frames

WebSocket communication is divided into **frames**.

A frame can carry:

* Text
* Binary data
* Control information

Common frame types include:

| Frame  | Purpose          |
| ------ | ---------------- |
| Text   | Text message     |
| Binary | Binary data      |
| Ping   | Connection check |
| Pong   | Response to ping |
| Close  | Close connection |

---

# 10. Ping/Pong

WebSockets have control frames for checking whether a connection is still alive.

```text
Server ── Ping ──→ Client
Server ←─ Pong ── Client
```

This can help detect broken connections.

---

# 11. Closing a WebSocket

Either side can request closure.

Conceptually:

```text
Client → Close
Server → Close
Connection closed
```

Proper close handling is important for application reliability.

---

# 12. Why WebSockets Matter in VAPT

WebSockets create a different attack surface from traditional HTTP endpoints.

A normal application might have:

```text
GET /profile
POST /login
POST /transfer
```

A WebSocket application may have:

```text
GET /socket
```

and then many different application messages travel through that single persistent connection.

Example:

```text
WebSocket
    │
    ├── chat message
    ├── private message
    ├── notification
    ├── user update
    └── administrative action
```

Therefore, **authorization and input validation must be applied to WebSocket messages too.**

---

# 13. Important Security Areas

When testing a WebSocket application in an authorized lab, examine:

### 1. Authentication

Can an unauthenticated user establish a connection?

```text
Anonymous → WebSocket
```

Could indicate an authentication issue depending on application requirements.

---

### 2. Authorization

This is one of the most important areas.

Suppose:

```text
User A
  ↓
WebSocket
  ↓
request: get_private_message(userB)
```

The server should verify that User A is authorized to access that resource.

Never assume:

> "The user connected successfully, therefore every message is allowed."

---

# 14. Message-Level Authorization

Consider:

```json
{
  "action": "get_profile",
  "user_id": 1001
}
```

A secure server should verify:

```text
Is current user allowed to access user_id 1001?
```

The server must **not blindly trust client-controlled identifiers**.

This is similar to authorization problems found in normal APIs.

---

# 15. Input Validation

WebSocket messages are still untrusted input.

Example:

```json
{
  "message": "hello"
}
```

The server should validate:

* Data type
* Length
* Allowed values
* Expected structure
* Authorization
* Business rules

---

# 16. Injection Risks

If WebSocket messages eventually reach another interpreter or backend system, unsafe input handling can create injection vulnerabilities.

Possible areas include:

```text
WebSocket
   ↓
Application
   ↓
Database
```

or:

```text
WebSocket
   ↓
Application
   ↓
Command execution
```

or:

```text
WebSocket
   ↓
Template rendering
```

The WebSocket itself isn't automatically vulnerable; the risk depends on how the application processes the message.

---

# 17. Cross-Site WebSocket Hijacking

A major concept to know is:

# CSWSH

**Cross-Site WebSocket Hijacking**

It can occur when a WebSocket endpoint relies on browser credentials such as cookies but does not properly validate the request's origin and other authentication controls.

Simplified idea:

```text
Victim Browser
      │
      │ authenticated cookies
      ▼
WebSocket Server

Malicious Site
      │
      └── attempts to initiate WebSocket connection
```

If the server incorrectly trusts the browser's credentials/origin context, an attacker may potentially interact with the victim's authenticated WebSocket session.

---

# 18. Origin Header

Browsers commonly send:

```http
Origin: https://example.com
```

A server can use origin validation as one part of its security controls.

For example:

```text
Allowed:
https://trusted.example

Rejected:
https://evil.example
```

### Important

Origin checking **is not a replacement for authentication and authorization**.

Use multiple security controls.

---

# 19. Cookies and WebSockets

WebSockets can participate in browser authentication flows.

For example:

```text
Browser
   │
   │ Cookie: session=...
   ▼
WebSocket handshake
```

This means WebSocket security and session security are closely related.

You should understand:

```text
Cookies
   ↓
Authentication
   ↓
WebSocket handshake
   ↓
Message authorization
```

---

# 20. CSRF vs WebSocket Security

Don't confuse these concepts.

### CSRF

Generally involves causing a victim's browser to make an unwanted authenticated request.

### CSWSH

Specifically concerns abusing WebSocket connections in a browser context, often involving authentication credentials and inadequate origin validation.

---

# 21. WebSocket Authentication

Common approaches include:

### Cookies

```text
Cookie → session authentication
```

### Token

```text
Authorization: Bearer <token>
```

### Application-level authentication

For example:

```json
{
  "action": "authenticate",
  "token": "..."
}
```

The exact implementation depends on the application.

---

# 22. WebSocket Security Testing Methodology

For an **authorized application/lab**, follow this workflow:

```text
1. Discover WebSocket endpoint
          ↓
2. Understand handshake
          ↓
3. Identify authentication
          ↓
4. Capture messages
          ↓
5. Understand message format
          ↓
6. Identify sensitive actions
          ↓
7. Test authorization
          ↓
8. Test input validation
          ↓
9. Test origin handling
          ↓
10. Check rate limiting
          ↓
11. Document findings
```

---

# 23. Burp Suite and WebSockets

Burp Suite is particularly useful for learning WebSocket security.

A typical workflow:

```text
Browser
   ↓
Burp Proxy
   ↓
WebSocket Server
```

You can inspect:

* Handshake
* WebSocket messages
* Direction
* Parameters
* Cookies
* Headers
* Responses

---

# 24. What to Look for in Burp

When examining a WebSocket connection, ask:

### Connection

```text
Where is the WebSocket endpoint?
```

### Authentication

```text
What authenticates the connection?
```

### Messages

```text
What actions can the client request?
```

### Authorization

```text
Can User A perform User B's actions?
```

### Input

```text
Does the server validate message parameters?
```

### Session

```text
What happens when authentication expires?
```

### Origin

```text
Does the server properly validate allowed origins?
```

---

# 25. Example WebSocket Message

Suppose the application sends:

```json
{
  "action": "send_message",
  "recipient": "user123",
  "message": "Hello"
}
```

Think like a pentester:

```text
Who can send?
      ↓
Who can receive?
      ↓
Can recipient be changed?
      ↓
Is message validated?
      ↓
Is message length limited?
      ↓
Is authorization checked server-side?
```

This is much more valuable than simply trying random payloads.

---

# 26. WebSocket vs REST API

| REST API                         | WebSocket                             |
| -------------------------------- | ------------------------------------- |
| Request/response                 | Persistent connection                 |
| HTTP methods                     | Messages/events                       |
| Stateless design is common       | Stateful connection is common         |
| Easy request-level authorization | Message-level authorization important |
| Good for standard APIs           | Good for real-time applications       |

---

# 27. WebSocket Security Headers

During the handshake, pay attention to:

```text
Origin
Cookie
Authorization
Sec-WebSocket-Key
Sec-WebSocket-Version
Sec-WebSocket-Protocol
```

Some headers are protocol-specific and should not be treated like ordinary application parameters.

---

# 28. Subprotocols

WebSockets can negotiate an application-level subprotocol.

Example:

```http
Sec-WebSocket-Protocol: chat
```

Server may respond with the selected protocol.

This can help define how messages are structured.

---

# 29. WebSocket Scalability

Persistent connections create operational challenges.

Imagine:

```text
10 users
   ↓
10 WebSockets

1,000 users
   ↓
1,000 WebSockets

100,000 users
   ↓
100,000 persistent connections
```

Therefore, servers need appropriate:

* Connection limits
* Resource management
* Timeouts
* Rate limiting
* Load balancing

---

# 30. Denial-of-Service Considerations

Because connections can remain open for long periods, applications should carefully manage:

* Maximum connections
* Message size
* Message rate
* Idle connections
* Server resources

A poorly designed WebSocket service can become vulnerable to resource exhaustion.

**Testing should only be performed against systems you own or are explicitly authorized to test.**

---

# 31. Secure WebSocket Architecture

A good architecture looks like:

```text
             Internet
                 │
                 ▼
          Reverse Proxy
                 │
          TLS / WSS
                 │
                 ▼
       WebSocket Application
                 │
       ┌─────────┴─────────┐
       ▼                   ▼
 Authentication       Authorization
       │                   │
       └─────────┬─────────┘
                 ▼
          Input Validation
                 │
                 ▼
             Backend
```

---

# 32. Secure WebSocket Checklist

For developers:

```text
[ ] Use WSS/TLS
[ ] Authenticate connections
[ ] Authorize every sensitive action
[ ] Validate every message
[ ] Validate message size
[ ] Implement rate limiting
[ ] Validate Origin where appropriate
[ ] Protect session credentials
[ ] Handle connection expiration
[ ] Log security-relevant events
[ ] Limit resources
[ ] Close invalid connections
```

---

# 33. Common Vulnerabilities

| Issue                    | Example                             |
| ------------------------ | ----------------------------------- |
| Missing authentication   | Anonymous connection accepted       |
| Broken authorization     | User accesses another user's data   |
| CSWSH                    | Improper browser-origin controls    |
| Injection                | Unsafe message processing           |
| DoS/resource exhaustion  | Excessive connections/messages      |
| Sensitive data exposure  | Secrets sent over insecure channel  |
| Weak session handling    | Expired session remains usable      |
| Missing input validation | Arbitrary unexpected message values |

---

# 34. Common Beginner Mistakes

### ❌ Mistake 1

Thinking:

> "WebSocket is just HTTP."

Not exactly.

It **starts with an HTTP-based upgrade handshake**, then communication switches to WebSocket framing.

---

### ❌ Mistake 2

Only testing the handshake.

The important application logic is often inside the messages sent **after** the handshake.

---

### ❌ Mistake 3

Assuming successful authentication means authorization is correct.

Authentication:

> Who are you?

Authorization:

> What are you allowed to do?

---

### ❌ Mistake 4

Trusting client-controlled IDs.

Example:

```json
{"user_id":1002}
```

The server must verify authorization.

---

### ❌ Mistake 5

Testing only HTTP endpoints.

A real application may have:

```text
HTTP API
+
WebSocket API
```

Both need security testing.

---

# 35. Practical Lab — Beginner

## Goal

Understand WebSocket communication.

Use a local WebSocket demo application or a deliberately vulnerable training lab.

Practice:

```text
Browser
 ↓
Connect
 ↓
Observe handshake
 ↓
Send message
 ↓
Receive response
```

Learn to identify:

* Endpoint
* Handshake
* Headers
* Messages
* Connection lifecycle

---

# 36. Practical Lab — Intermediate

Build a simple local WebSocket application.

Architecture:

```text
Python WebSocket Server
          ↑
          │
       Browser
```

Implement:

```text
/connect
/send
/receive
```

Then add:

1. Authentication
2. User IDs
3. Message validation
4. Authorization
5. Rate limiting

This teaches both **development and security**.

---

# 37. Practical Lab — VAPT

Create two local users:

```text
Alice
Bob
```

Application:

```text
Alice → WebSocket → Server
Bob   → WebSocket → Server
```

Test whether:

```text
Alice can access Bob's private data
```

The goal is to understand **authorization testing**, not simply to find random payloads.

---

# 38. Advanced Topics to Learn Next

Once the basics are strong, study:

### Networking

* TCP
* TLS
* HTTP/1.1
* HTTP/2
* WebSocket framing

### Web security

* Authentication
* Authorization
* CSRF
* CORS
* Session management

### WebSocket-specific

* CSWSH
* Origin validation
* Message authorization
* Subprotocols
* Connection management
* Rate limiting

### Tools

* Burp Suite
* Browser DevTools
* Wireshark
* Python WebSocket libraries

---

# 39. Interview Questions

### Q1. What is WebSocket?

A protocol providing persistent, bidirectional communication between client and server.

### Q2. What is `ws://`?

Non-TLS WebSocket communication.

### Q3. What is `wss://`?

WebSocket communication protected by TLS.

### Q4. Which HTTP status indicates successful protocol upgrade?

**101 Switching Protocols**

### Q5. Does WebSocket use HTTP forever?

No. HTTP is used for the initial upgrade handshake; communication then uses WebSocket framing.

### Q6. What is CSWSH?

Cross-Site WebSocket Hijacking, where weaknesses in browser-origin/authentication handling can allow abuse of authenticated WebSocket interactions.

### Q7. Why is authorization important for WebSockets?

Because a successfully connected user must still be restricted to actions/data they are authorized to access.

### Q8. What tool can intercept WebSocket traffic?

**Burp Suite** is commonly used for authorized web-security testing.

### Q9. What is the difference between authentication and authorization?

```text
Authentication = Who are you?
Authorization  = What can you do?
```

### Q10. Why is input validation important?

WebSocket messages are untrusted input and can reach sensitive application logic.

---

# 40. Scenario-Based Questions

### Scenario 1

A WebSocket endpoint accepts a connection without authentication.

What should you investigate?

**Answer:** Whether anonymous WebSocket access is intended and whether sensitive actions/data are available without authentication.

---

### Scenario 2

User A changes:

```json
{"user_id":"A"}
```

to:

```json
{"user_id":"B"}
```

and receives B's private data.

What security concept is involved?

**Answer:** Broken access control / authorization failure.

---

### Scenario 3

The server accepts WebSocket connections from unexpected website origins while relying on browser cookies.

What should you investigate?

**Answer:** Potential CSWSH risk and whether proper origin/authentication protections exist.

---

# 41. MCQs

### 1. WebSocket provides:

A. Only file transfer
B. Persistent bidirectional communication
C. DNS resolution
D. IP routing

**Answer: B**

---

### 2. `wss://` represents:

A. WebSocket without TLS
B. WebSocket over TLS
C. FTP over TLS
D. SSH

**Answer: B**

---

### 3. Successful WebSocket upgrade commonly returns:

A. 200
B. 301
C. 404
D. 101

**Answer: D**

---

### 4. Which header indicates a WebSocket upgrade?

A. Content-Type
B. Upgrade
C. Referer
D. Accept-Encoding

**Answer: B**

---

### 5. Authentication answers:

A. What can you do?
B. Who are you?
C. What port is open?
D. What protocol is used?

**Answer: B**

---

### 6. Authorization answers:

A. Who are you?
B. What are you allowed to do?
C. What is your IP?
D. What is DNS?

**Answer: B**

---

### 7. CSWSH is related to:

A. DNS
B. WebSocket browser abuse
C. FTP
D. DHCP

**Answer: B**

---

### 8. Which tool is useful for inspecting WebSocket traffic?

A. Burp Suite
B. Calculator
C. Paint
D. Notepad

**Answer: A**

---

### 9. WebSocket messages should be:

A. Trusted automatically
B. Validated by the server
C. Ignored
D. Always encrypted with Base64

**Answer: B**

---

### 10. Which is generally preferred for production WebSocket traffic?

A. FTP
B. ws://
C. wss://
D. Telnet

**Answer: C**

---

# 42. Must Remember 🔥

```text
WebSocket
   ↓
Persistent communication
   ↓
Bidirectional
   ↓
HTTP Upgrade handshake
   ↓
101 Switching Protocols
   ↓
WebSocket frames/messages
```

### Memorize:

```text
ws://  → WebSocket
wss:// → WebSocket + TLS

101 → Switching Protocols

Authentication → Who are you?
Authorization  → What can you do?

Origin validation
       +
Authentication
       +
Authorization
       +
Input validation
       +
Rate limiting
       =
Stronger WebSocket security
```

---

# 43. Final Mental Model

Think of a WebSocket application as:

```text
                 WEB APPLICATION
                       │
             ┌─────────┴─────────┐
             │                   │
            HTTP             WebSocket
             │                   │
       Request/Response      Persistent
                                 │
                          ┌──────┴──────┐
                          │             │
                       Client        Server
                          │             │
                          └─────⇄───────┘
                                 │
                         Application Logic
                                 │
                    ┌────────────┼────────────┐
                    ▼            ▼            ▼
               Authentication Authorization Validation
```

The key VAPT mindset is:

> **Don't just test whether the WebSocket connects. Test what an authenticated user can actually do through the connection.**

---

# Key Takeaways

* WebSockets provide **persistent, bidirectional communication**.
* The connection normally begins with an **HTTP Upgrade handshake**.
* `101 Switching Protocols` indicates a successful upgrade.
* `ws://` is non-TLS; `wss://` uses TLS.
* WebSocket messages are **untrusted input**.
* Authentication and authorization must be handled correctly.
* **Message-level authorization** is especially important.
* CSWSH is an important WebSocket-specific security concept.
* Burp Suite is useful for authorized WebSocket testing.
* Rate limiting and connection/resource controls help prevent abuse.
* WebSocket security should be considered alongside normal HTTP/API security.
