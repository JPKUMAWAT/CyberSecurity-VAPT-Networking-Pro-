

# 1. Why Cookies & Sessions Matter

HTTP is **stateless**.

That means:

```text
Request 1 → Server
Request 2 → Server
Request 3 → Server
```

By default, the server doesn't automatically know that all three requests came from the same logged-in user.

Web applications use mechanisms such as:

* Cookies
* Sessions
* Session IDs
* Authentication tokens

to maintain state.

### Simple example

You log in:

```text
Username: alice
Password: ********
        ↓
      Server
        ↓
Authentication successful
        ↓
Session created
        ↓
Browser receives session cookie
```

Later:

```text
Browser
   |
   | Cookie: session=abc123
   ↓
Server
   |
   | "This session belongs to Alice"
   ↓
Protected page
```

---

# 2. Cookie vs Session

These terms are related but **not the same**.

| Cookie                                      | Session                                       |
| ------------------------------------------- | --------------------------------------------- |
| Stored by browser/client                    | Usually maintained by server                  |
| Sent with matching requests                 | Represents authenticated/stateful interaction |
| Can contain data or an identifier           | Often identified using a session ID           |
| Controlled partly through cookie attributes | Server controls session lifecycle             |
| Example: `sessionid=abc123`                 | Server maps `abc123 → user Alice`             |

A common architecture is:

```text
Browser Cookie
      |
      | session_id=ABC123
      ↓
Web Server
      |
      | ABC123 → Alice
      ↓
User Account
```

**Important:** A cookie called `session` does not necessarily mean the actual session data is stored inside the cookie.

---

# 3. What Is a Cookie?

A cookie is a small piece of data that a server asks the browser to store.

Example:

```http
Set-Cookie: session_id=abc123
```

The browser may later send:

```http
Cookie: session_id=abc123
```

---

# 4. Cookie Lifecycle

## Step 1 — Server sends cookie

```http
HTTP/1.1 200 OK
Set-Cookie: session_id=abc123
```

## Step 2 — Browser stores it

```text
Browser
└── session_id = abc123
```

## Step 3 — Browser sends it back

```http
GET /dashboard HTTP/1.1
Host: example.test
Cookie: session_id=abc123
```

## Step 4 — Server validates it

```text
session_id
    ↓
Server-side session store
    ↓
User = Alice
    ↓
Allow dashboard
```

---

# 5. Important Cookie Attributes

Cookie security depends heavily on its attributes.

## 5.1 Secure

```http
Set-Cookie: session_id=abc123; Secure
```

### Meaning

Browser should send the cookie over **HTTPS** connections.

### Why important?

Without HTTPS protection, authentication cookies could potentially be exposed over an insecure network.

### VAPT relevance

Check whether sensitive authentication cookies are missing the `Secure` attribute.

---

# 6. HttpOnly

Example:

```http
Set-Cookie: session_id=abc123; HttpOnly
```

### Meaning

JavaScript normally cannot access the cookie through APIs such as:

```javascript
document.cookie
```

### Security benefit

It helps reduce the ability of client-side JavaScript to directly read sensitive cookies.

### Important limitation

`HttpOnly` does **not** stop XSS.

It mainly prevents JavaScript from directly reading that cookie.

For example:

```text
XSS
 |
 +----→ execute JavaScript
 |
 +----→ directly read HttpOnly cookie? ❌
```

But an XSS vulnerability can still perform actions as the victim within the browser's security context.

**Must Remember:**

> HttpOnly is a mitigation for cookie theft through JavaScript, not a complete XSS defense.

---

# 7. SameSite

`SameSite` controls when cookies are sent in cross-site requests.

Common values:

```text
SameSite=Strict
SameSite=Lax
SameSite=None
```

## Strict

More restrictive cross-site behavior.

```http
Set-Cookie: session_id=abc123; SameSite=Strict
```

## Lax

Allows some cross-site navigation scenarios while restricting many cross-site requests.

Often a practical default.

## None

Cookie can be sent in cross-site contexts.

When using:

```text
SameSite=None
```

the cookie generally also needs:

```text
Secure
```

### VAPT relevance

SameSite is an important part of the browser-side defense against **CSRF**, but it should not be treated as the only CSRF defense.

---

# 8. Secure Cookie Configuration

A sensitive session cookie might look like:

```http
Set-Cookie: session_id=abc123; Secure; HttpOnly; SameSite=Lax
```

Think:

```text
Secure
   ↓
HTTPS transport

HttpOnly
   ↓
Reduce JavaScript access

SameSite
   ↓
Control cross-site cookie sending
```

---

# 9. Cookie Domain and Path

Cookies can also contain:

```text
Domain
Path
Expires
Max-Age
```

Example:

```http
Set-Cookie: session_id=abc123;
Path=/;
Secure;
HttpOnly;
SameSite=Lax
```

### Path

Controls which URL paths receive the cookie.

Example:

```text
Path=/admin
```

means the cookie is intended for requests under that path.

### Domain

Controls which host/domain can receive the cookie.

Poorly scoped cookies can increase exposure across subdomains.

---

# 10. Session Management

A typical login flow:

```text
        LOGIN
          |
          ↓
+-------------------+
| Username/Password |
+-------------------+
          |
          ↓
      Web Server
          |
          ↓
 Authentication
          |
       SUCCESS
          |
          ↓
   Generate session
          |
          ↓
 session_id = XYZ
          |
          ↓
       Browser
```

Then:

```text
Browser
   |
   | Cookie: session_id=XYZ
   ↓
Server
   |
   | Validate XYZ
   ↓
Authenticated User
```

---

# 11. Session ID

A session ID should be:

* Unpredictable
* Sufficiently random
* Unique
* Properly protected
* Invalidated when appropriate

Bad example:

```text
session=1001
session=1002
session=1003
```

An attacker may be able to predict values.

Better:

```text
session=7f92c8d1a...random...
```

The exact format depends on the application/framework.

---

# 12. Session Fixation

## Concept

Session fixation occurs when an attacker can cause a victim to use a session identifier that the attacker already knows.

Conceptually:

```text
Attacker
   |
   | Known session ID
   ↓
Application
   |
   ↓
Victim uses same session
   |
   ↓
Victim authenticates
```

If the application fails to rotate the session identifier after authentication, the attacker may be able to abuse that session.

### Correct defense

After successful authentication:

```text
Before login:
Session = A

       ↓ LOGIN

After login:
Session = B
```

The session identifier should be regenerated.

---

# 13. Session Hijacking

Session hijacking means obtaining or abusing another user's valid authenticated session.

Conceptually:

```text
Victim
   |
   | Valid session
   ↓
Server

Attacker
   |
   | Somehow obtains/uses session
   ↓
Server
```

Potential causes include:

* Insecure transport
* XSS
* Malware/endpoint compromise
* Poor session protection
* Weak session management

### Important

Session hijacking is different from stealing a username/password.

The attacker may be using an already authenticated session.

---

# 14. Session Expiration

A secure application should have sensible session lifetime policies.

Example:

```text
Login
 ↓
Session created
 ↓
Activity
 ↓
Timeout
 ↓
Session invalidated
```

Two useful concepts:

### Idle timeout

Session expires after inactivity.

### Absolute timeout

Session expires after a maximum lifetime even if the user remains active.

---

# 15. Logout and Session Invalidation

A secure logout should invalidate the server-side session where applicable.

Example:

```text
Before logout:

session_id = ABC123
       ↓
Server → Valid

After logout:

session_id = ABC123
       ↓
Server → Invalid
```

Simply hiding the UI or deleting a browser cookie is not necessarily sufficient if the server-side session remains valid.

---

# 16. Session Cookie Deletion

A server can instruct the browser to remove a cookie by setting an expiration in the past or using an appropriate `Max-Age`.

Example:

```http
Set-Cookie: session_id=;
Max-Age=0;
Path=/;
```

But again:

> Client-side cookie deletion and server-side session invalidation are separate concepts.

---

# 17. Authentication vs Authorization

This is a **very important interview concept**.

### Authentication

> Who are you?

Example:

```text
Username + Password
        ↓
Authentication
        ↓
Alice
```

### Authorization

> What are you allowed to do?

Example:

```text
Alice
 ↓
Role = User
 ↓
Can access /profile
Cannot access /admin
```

Remember:

```text
Authentication = Identity
Authorization  = Permission
```

---

# 18. Cookie-Based Authentication

Example:

```text
POST /login

username=alice
password=******
```

Server:

```text
200 OK
Set-Cookie: session_id=ABC123
```

Next request:

```http
GET /account

Cookie: session_id=ABC123
```

Server:

```text
ABC123
 ↓
Alice
 ↓
Authenticated
```

---

# 19. Token-Based Authentication

Not every application uses traditional server-side sessions.

Some applications use tokens.

Example:

```http
Authorization: Bearer <token>
```

Conceptually:

```text
Client
   |
   | Bearer token
   ↓
API
   |
   ↓
Validate token
   |
   ↓
Identity + permissions
```

Examples of token systems include:

* JWT
* OAuth 2.0 access tokens
* API tokens

These have different security considerations from traditional cookie sessions.

---

# 20. Cookie vs Bearer Token

| Cookie Session                               | Bearer Token                             |
| -------------------------------------------- | ---------------------------------------- |
| Common in browser applications               | Common in APIs                           |
| Browser automatically sends matching cookies | Client usually explicitly sends token    |
| Cookie attributes matter                     | Token storage/handling matters           |
| CSRF can be relevant                         | CSRF depends on how token is transported |
| Often server-side session state              | May be stateless or stateful             |

---

# 21. CSRF

## Cross-Site Request Forgery

CSRF abuses the browser's ability to automatically attach authentication credentials to a request.

Concept:

```text
Victim Browser
      |
      | Automatically sends cookie
      ↓
Target Website
```

An attacker-controlled website may try to cause the victim's browser to make an unwanted request.

### Example concept

Victim is logged into:

```text
bank.example
```

Then visits another website.

If the application's authentication mechanism automatically accompanies a state-changing request and there is insufficient CSRF protection, the request may be accepted as the victim.

---

# 22. CSRF Defenses

Common defenses include:

### 1. CSRF tokens

```text
Request
 ├── Session Cookie
 └── CSRF Token
```

Server verifies both.

### 2. SameSite cookies

Can reduce cross-site cookie sending.

### 3. Origin/Referer validation

Applications can validate request origin where appropriate.

### 4. Avoid state-changing GET requests

Prefer:

```text
POST /change-email
```

rather than:

```text
GET /change-email?email=...
```

---

# 23. XSS vs CSRF

Very common confusion.

| XSS                                                             | CSRF                                                        |
| --------------------------------------------------------------- | ----------------------------------------------------------- |
| Injects/executes attacker-controlled script in victim's browser | Tricks browser into making an unwanted request              |
| Main issue is code execution in application origin              | Main issue is unauthorized state-changing action            |
| Can sometimes access non-HttpOnly data                          | Usually relies on browser automatically sending credentials |
| Output/input handling is important                              | CSRF defenses are important                                 |

### Mental model

```text
XSS
Attacker-controlled JavaScript
          ↓
Victim's browser
          ↓
Target origin


CSRF
Attacker-controlled request trigger
          ↓
Victim's browser
          ↓
Target application
```

---

# 24. Session Security Checklist — VAPT

When testing an authorized application, examine:

### Cookie attributes

```text
☐ Secure
☐ HttpOnly
☐ SameSite
☐ Appropriate Domain
☐ Appropriate Path
```

### Session lifecycle

```text
☐ New session after login
☐ Session rotation after privilege changes
☐ Logout invalidates session
☐ Session expiration works
☐ Password reset invalidates appropriate sessions
```

### Session identifiers

```text
☐ High entropy
☐ Unpredictable
☐ Unique
☐ Not exposed unnecessarily
```

### Application behavior

```text
☐ Authentication enforced
☐ Authorization enforced
☐ CSRF protections where required
☐ HTTPS enforced
☐ No sensitive data unnecessarily stored in cookies
```

---

# 25. Safe Lab: Inspect Cookies

Use a deliberately vulnerable/local application or an application you are authorized to test.

In a browser:

```text
Developer Tools
      ↓
Application / Storage
      ↓
Cookies
```

Inspect:

```text
Name
Value
Domain
Path
Expires
Secure
HttpOnly
SameSite
```

### Goal

Don't just look at the cookie.

Ask:

> **Why does this cookie exist, who receives it, when is it sent, and what happens if it is invalidated?**

That is pentester thinking.

---

# 26. Inspect Cookies Through HTTP Headers

Example:

```http
HTTP/1.1 200 OK
Set-Cookie: session_id=abc123; Secure; HttpOnly; SameSite=Lax
```

Look for:

```text
Set-Cookie
```

Then inspect later requests:

```http
Cookie: session_id=abc123
```

This teaches the complete lifecycle.

---

# 27. Burp Suite Workflow

For an authorized lab:

```text
Browser
   ↓
Burp Proxy
   ↓
Web Application
```

You can inspect:

```text
Request
 ├── Cookie
 ├── Authorization
 ├── Method
 └── Parameters

Response
 ├── Set-Cookie
 ├── Status
 └── Headers
```

### Useful Burp tasks

* Observe login flow
* Identify session cookie
* Compare pre/post-login cookies
* Observe logout behavior
* Inspect cookie attributes
* Understand session rotation
* Test authorization in a lab

---

# 28. Common Session Security Findings

Potential findings include:

### Missing Secure flag

Sensitive cookie may be sent without HTTPS protection.

### Missing HttpOnly

Sensitive cookie is directly accessible to JavaScript.

### Weak SameSite configuration

May increase CSRF exposure depending on application behavior.

### Session fixation

Session identifier does not rotate appropriately after authentication.

### Session not invalidated on logout

Old session remains usable.

### Excessively long session lifetime

Session remains valid longer than necessary.

### Predictable session IDs

Could weaken session security.

---

# 29. Important Distinction: Cookie ≠ Authentication Always

Not every cookie authenticates a user.

Example:

```text
theme=dark
language=en
cart_id=123
analytics_id=abc
```

These may simply store preferences or tracking information.

Therefore:

> Don't assume every cookie is a session cookie.

---

# 30. Important Distinction: Session ≠ Cookie

A session can exist without the browser storing the entire session data in a cookie.

Typical design:

```text
Browser
Cookie:
session_id=ABC
       |
       ↓
Server
Session Store:
ABC → User Alice
    → Role User
    → Login time
```

The cookie contains only the identifier.

---

# 31. Security Architecture Example

A secure-ish basic design:

```text
             HTTPS
Browser ─────────────────→ Web Server
   │                           │
   │ Cookie: session_id        │
   │ Secure                    │
   │ HttpOnly                  │
   │ SameSite=Lax              │
   │                           │
   │                           ↓
   │                     Session Store
   │                           │
   │                           ↓
   │                     User Identity
   │                           │
   │                           ↓
   │                     Authorization
```

---

# 32. Advanced Concepts to Learn Next

Once this lesson is strong, learn:

### Session Management

* Session rotation
* Session invalidation
* Session timeout
* Concurrent sessions

### Browser Security

* Same-Origin Policy
* CORS
* SameSite
* Secure Contexts

### Authentication

* MFA
* OAuth 2.0
* OpenID Connect
* JWT
* Refresh tokens

### Web Attacks

* CSRF
* XSS
* Session fixation
* Session hijacking
* Authentication bypass
* Broken access control

---

# 33. Real VAPT Mental Model

When you encounter a session cookie, think:

```text
1. Where is it created?
        ↓
2. How random is it?
        ↓
3. How is it transported?
        ↓
4. Who receives it?
        ↓
5. Can JavaScript access it?
        ↓
6. How long does it remain valid?
        ↓
7. Does login rotate it?
        ↓
8. Does logout invalidate it?
        ↓
9. Are authorization checks separate?
        ↓
10. Can cross-site requests abuse it?
```

This is much more valuable than simply memorizing cookie flags.

---

# 34. Common Beginner Mistakes

❌ Thinking `HttpOnly` prevents XSS.

✅ It mainly prevents JavaScript from directly reading the cookie.

---

❌ Thinking `Secure` encrypts the cookie itself.

✅ It tells the browser to send the cookie only over HTTPS.

---

❌ Thinking SameSite completely prevents CSRF.

✅ It can reduce CSRF risk, but applications may need additional defenses.

---

❌ Thinking logout always invalidates every session.

✅ Test the application's actual session invalidation behavior.

---

❌ Thinking cookie = session.

✅ A cookie may contain a session identifier, but cookies and sessions are different concepts.

---

❌ Thinking authentication and authorization are the same.

✅ Authentication identifies the user; authorization determines what they can do.

---

# 35. Interview Questions

### Q1. What is a cookie?

A small piece of data stored by the browser and associated with a website.

### Q2. Why are sessions used?

To maintain application state across otherwise stateless HTTP requests.

### Q3. What does HttpOnly do?

It prevents normal JavaScript access to the cookie.

### Q4. What does Secure do?

It restricts the cookie to secure HTTPS transmission.

### Q5. What is SameSite?

A cookie attribute controlling cross-site cookie sending behavior.

### Q6. What is session fixation?

An attack where an attacker attempts to make a victim use a session identifier known to the attacker.

### Q7. What is session hijacking?

Abusing another user's valid authenticated session.

### Q8. Authentication vs authorization?

```text
Authentication → Who are you?
Authorization  → What can you do?
```

### Q9. What is CSRF?

An attack where a victim's browser is induced to perform an unwanted action against a site where the victim is authenticated.

### Q10. Why should sessions rotate after login?

To prevent an attacker-controlled/pre-authentication session identifier from remaining valid after authentication.

---

# 36. Scenario-Based Questions

### Scenario 1

You see:

```http
Set-Cookie: session=abc123
```

with no other security attributes.

**What should you investigate?**

Check:

```text
Secure
HttpOnly
SameSite
Domain
Path
HTTPS enforcement
```

---

### Scenario 2

User logs out, but the old authenticated session still works.

**Potential issue?**

Session invalidation failure.

---

### Scenario 3

Session ID before login:

```text
ABC
```

Session ID after login:

```text
ABC
```

**What should you investigate?**

Possible session fixation risk; determine whether the application properly rotates the session after authentication.

---

### Scenario 4

Cookie:

```http
HttpOnly
```

XSS exists.

**Does HttpOnly mean XSS is harmless?**

No.

XSS can still execute JavaScript in the application's security context and potentially perform actions depending on the application's defenses.

---

# 37. MCQs

### 1. HTTP is normally:

A. Stateful
B. Stateless
C. Encrypted
D. Connectionless only

**Answer: B**

---

### 2. Which header creates/updates a cookie?

A. Cookie
B. Set-Cookie
C. Session
D. Authorization

**Answer: B**

---

### 3. Which attribute restricts cookie transmission to HTTPS?

A. HttpOnly
B. SameSite
C. Secure
D. Path

**Answer: C**

---

### 4. Which attribute restricts normal JavaScript access?

A. Secure
B. HttpOnly
C. Domain
D. Path

**Answer: B**

---

### 5. Authentication answers:

A. What can you access?
B. Who are you?
C. Which port is open?
D. What is your IP?

**Answer: B**

---

### 6. Authorization answers:

A. Who are you?
B. What are you allowed to do?
C. What is your password?
D. What is your session ID?

**Answer: B**

---

### 7. CSRF primarily abuses:

A. DNS resolution
B. Browser authentication context
C. TCP handshake
D. File permissions

**Answer: B**

---

### 8. Session fixation is primarily related to:

A. DNS
B. Session identifier lifecycle
C. IP addressing
D. Encryption algorithms

**Answer: B**

---

### 9. A good session ID should be:

A. Predictable
B. Sequential
C. Random and unpredictable
D. Based on username

**Answer: C**

---

### 10. Which is NOT necessarily a session cookie?

A. `session_id=ABC`
B. `cart_id=123`
C. `auth_token=XYZ`
D. `sid=ABC`

**Answer: B**

---

### 11. What should happen to a session after logout?

A. Remain valid forever
B. Ideally become invalid
C. Become predictable
D. Change username

**Answer: B**

---

### 12. SameSite is mainly related to:

A. Cross-site cookie behavior
B. IP routing
C. DNS
D. TCP reliability

**Answer: A**

---

### 13. A session ID identifies:

A. Always the user's IP
B. A session maintained by the application
C. A DNS record
D. A TCP port

**Answer: B**

---

### 14. `Cookie:` is normally sent by:

A. Server
B. Browser/client
C. DNS server
D. Firewall

**Answer: B**

---

### 15. `Set-Cookie:` is normally sent by:

A. Server
B. Browser
C. Router
D. DNS server

**Answer: A**

---

# 38. Must Remember 🔥

```text
HTTP
 ↓
Stateless

Cookie
 ↓
Browser-side data

Session
 ↓
Application state

Set-Cookie
 ↓
Server → Browser

Cookie
 ↓
Browser → Server

Secure
 ↓
HTTPS only

HttpOnly
 ↓
Reduce JavaScript access

SameSite
 ↓
Cross-site cookie behavior

Authentication
 ↓
Who are you?

Authorization
 ↓
What can you do?

CSRF
 ↓
Unwanted authenticated request

Session Fixation
 ↓
Known/preselected session identifier

Session Hijacking
 ↓
Abuse of another user's valid session
```

---

# 39. Practical Lab Progression

Only use applications you own or are explicitly authorized to test.

### 🟢 Beginner

1. Open browser Developer Tools.
2. Open a local test application.
3. Log in.
4. Inspect cookies.
5. Identify `Set-Cookie`.
6. Identify the subsequent `Cookie` request header.

### 🟡 Intermediate

Build a small local Flask application and experiment with:

```text
Secure
HttpOnly
SameSite
Session timeout
Logout
Session rotation
```

### 🔴 VAPT

Using a legal training lab:

```text
Login
 ↓
Capture request
 ↓
Identify session mechanism
 ↓
Inspect cookie attributes
 ↓
Compare pre/post-login session IDs
 ↓
Test logout invalidation
 ↓
Check authorization separately
 ↓
Check CSRF protection
 ↓
Document evidence
 ↓
Assess impact
 ↓
Write remediation
```

---

# 40. Final Mental Model

Don't memorize:

> "Cookie = login."

Instead remember:

```text
                WEB APPLICATION
                      │
                      ↓
                 Authentication
                      │
                 "Who are you?"
                      │
                      ↓
                 Session/Token
                      │
                      ↓
             Browser Credential
                      │
             ┌────────┴────────┐
             ↓                 ↓
           Cookie             Token
             │                 │
       Secure/HttpOnly       Storage/
       SameSite/etc.         handling
             │                 │
             └────────┬────────┘
                      ↓
                 Authorization
                      │
               "What can you do?"
                      │
                      ↓
                  Resource
```

### The key VAPT question

Whenever you see authentication/session behavior, ask:

> **Can an attacker obtain, predict, reuse, fix, or improperly influence the credential that represents the user's authenticated state?**

If you understand that question deeply, you're moving from **web-security memorization → actual VAPT reasoning**.

