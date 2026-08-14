# 1. What is HTTP?

**HTTP = HyperText Transfer Protocol**

HTTP is an **application-layer protocol** used for communication between clients and web servers.

Typical communication:

```text
Browser / Client
      │
      │ HTTP Request
      ▼
   Web Server
      │
      │ HTTP Response
      ▼
Browser / Client
```

Example:

```text
Client → GET /login HTTP/1.1
Server → HTTP/1.1 200 OK
```

HTTP itself does not define how packets physically travel across the network. Lower layers such as TCP/IP handle transport and delivery.

---

# 2. Where HTTP Fits in the Network Model

Using the TCP/IP model:

```text
┌──────────────────────────────┐
│ Application                  │
│ HTTP / HTTPS / DNS / SSH     │
├──────────────────────────────┤
│ Transport                    │
│ TCP / UDP                    │
├──────────────────────────────┤
│ Internet                     │
│ IP                           │
├──────────────────────────────┤
│ Network Access               │
│ Ethernet / Wi-Fi             │
└──────────────────────────────┘
```

HTTP operates at the **Application layer**.

For traditional HTTP/1.1 and HTTP/2, TCP is normally used.

HTTP/3 uses **QUIC over UDP**.

---

# 3. HTTP vs HTTPS

| HTTP                                                                   | HTTPS                                      |
| ---------------------------------------------------------------------- | ------------------------------------------ |
| Plain HTTP                                                             | HTTP protected by TLS                      |
| Usually port 80                                                        | Usually port 443                           |
| Data can be observed/modified by an attacker who can intercept traffic | TLS provides confidentiality and integrity |
| `http://`                                                              | `https://`                                 |

Important:

> HTTPS does **not** mean the website itself is trustworthy.

HTTPS protects the communication channel, but a vulnerable application can still have SQL injection, XSS, broken access control, etc.

---

# 4. What Happens When You Open a Website?

Suppose you enter:

```text
https://example.com/login
```

A simplified process is:

```text
1. Browser parses URL
        ↓
2. DNS resolves example.com → IP address
        ↓
3. Connection is established
        ↓
4. TLS handshake happens for HTTPS
        ↓
5. Browser sends HTTP request
        ↓
6. Web server receives request
        ↓
7. Application processes request
        ↓
8. Server generates response
        ↓
9. Browser receives response
        ↓
10. Browser renders the page
```

The exact sequence varies depending on protocol version, caching, connection reuse, HTTP/3, DNS caching, etc.

---

# 5. Anatomy of a URL

Example:

```text
https://example.com:443/login?user=admin#profile
```

Breakdown:

```text
https://
   ↓
Scheme

example.com
   ↓
Host

:443
   ↓
Port

/login
   ↓
Path

?user=admin
   ↓
Query string

#profile
   ↓
Fragment
```

## Important

The URL fragment:

```text
#profile
```

is normally processed by the browser and is **not sent to the server in the HTTP request**.

---

# 6. HTTP Request

An HTTP request is sent by the client to the server.

Example:

```http
GET /login HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Accept: text/html
Connection: close
```

A request generally contains:

```text
Request Line
     ↓
Headers
     ↓
Blank Line
     ↓
Optional Body
```

---

# 7. Request Line

The first line is called the **request line**.

Example:

```http
GET /login HTTP/1.1
```

It contains:

```text
METHOD   PATH   HTTP VERSION
```

Therefore:

```text
GET
 ↓
Method

/login
 ↓
Resource

HTTP/1.1
 ↓
Protocol version
```

---

# 8. HTTP Headers

Headers provide metadata about the request.

Example:

```http
Host: example.com
User-Agent: Mozilla/5.0
Accept: text/html
Accept-Language: en-US
Cookie: session=abc123
```

Common headers:

| Header         | Purpose                          |
| -------------- | -------------------------------- |
| Host           | Specifies target host            |
| User-Agent     | Identifies client software       |
| Accept         | Content types client accepts     |
| Content-Type   | Type of request body             |
| Content-Length | Body size                        |
| Authorization  | Authentication credentials/token |
| Cookie         | Sends stored cookies             |
| Referer        | Indicates referring URL          |
| Origin         | Indicates request origin         |
| Cache-Control  | Caching behavior                 |

---

# 9. HTTP Request Body

Some requests contain data in the body.

Example:

```http
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 27

username=alice&password=test
```

The body contains application data.

Commonly used with:

```text
POST
PUT
PATCH
```

But technically HTTP methods are not restricted to whether a body can exist.

---

# 10. GET Request

GET normally requests a resource.

Example:

```http
GET /products HTTP/1.1
Host: example.com
```

Query parameters may appear in the URL:

```text
/products?id=10
```

Request:

```http
GET /products?id=10 HTTP/1.1
Host: example.com
```

### VAPT relevance

Pentesters inspect query parameters for issues such as:

* IDOR/BOLA
* reflected XSS
* injection
* information disclosure
* improper input validation

Only test systems you are authorized to assess.

---

# 11. POST Request

POST commonly sends data to the server.

Example:

```http
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

username=alice&password=test
```

The server processes the submitted data.

Common uses:

```text
Login
Registration
Form submission
File upload
API operations
```

---

# 12. HTTP Response

The server responds to the client.

Example:

```http
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1250

<html>
...
</html>
```

Response structure:

```text
Status Line
     ↓
Headers
     ↓
Blank Line
     ↓
Body
```

---

# 13. Response Status Line

Example:

```http
HTTP/1.1 200 OK
```

It contains:

```text
HTTP Version
     ↓
Status Code
     ↓
Reason Phrase
```

---

# 14. Important HTTP Status Codes

## 2xx — Success

```text
200 OK
201 Created
204 No Content
```

Meaning:

```text
Request succeeded
```

---

## 3xx — Redirection

```text
301 Moved Permanently
302 Found
304 Not Modified
307 Temporary Redirect
308 Permanent Redirect
```

---

## 4xx — Client-side/request errors

```text
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
405 Method Not Allowed
429 Too Many Requests
```

---

## 5xx — Server-side errors

```text
500 Internal Server Error
502 Bad Gateway
503 Service Unavailable
504 Gateway Timeout
```

---

# 15. 401 vs 403 — VERY IMPORTANT

### 401 Unauthorized

Usually means:

> Authentication is required or authentication failed.

Example:

```text
GET /account
→ 401 Unauthorized
```

### 403 Forbidden

Usually means:

> The server understood the request but refuses to authorize it.

Example:

```text
GET /admin
→ 403 Forbidden
```

Easy memory:

```text
401 → Authentication problem
403 → Authorization/access problem
```

---

# 16. HTTP Response Headers

Example:

```http
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 5000
Set-Cookie: session=abc
Cache-Control: no-store
Strict-Transport-Security: max-age=31536000
```

Important security-related headers include:

```text
Content-Security-Policy
Strict-Transport-Security
X-Content-Type-Options
Referrer-Policy
Permissions-Policy
Set-Cookie
Cache-Control
```

---

# 17. HTTP Content-Type

`Content-Type` tells the receiver what type of data is being sent.

Examples:

```http
Content-Type: text/html
```

HTML response.

```http
Content-Type: application/json
```

JSON API response.

```http
Content-Type: application/x-www-form-urlencoded
```

Form-style request data.

```http
Content-Type: multipart/form-data
```

Often used for forms/file uploads.

---

# 18. JSON API Example

Modern applications frequently communicate using JSON.

Request:

```http
POST /api/login HTTP/1.1
Host: example.com
Content-Type: application/json

{
  "username": "alice",
  "password": "example"
}
```

Response:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "success": true,
  "message": "Login successful"
}
```

### VAPT relevance

API testing commonly involves examining:

```text
Authentication
Authorization
Input validation
Object-level access control
Rate limiting
Error handling
Sensitive data exposure
```

---

# 19. Cookies in HTTP

HTTP is fundamentally **stateless**.

That means each request does not automatically remember previous requests.

Applications commonly use cookies to maintain state.

Server:

```http
Set-Cookie: session=abc123
```

Browser later sends:

```http
Cookie: session=abc123
```

Flow:

```text
Login
  ↓
Server creates session
  ↓
Set-Cookie
  ↓
Browser stores cookie
  ↓
Future requests include Cookie
  ↓
Server identifies session
```

Security attributes include:

```text
Secure
HttpOnly
SameSite
```

These are important for web security and will be covered more deeply in the Cookies/Sessions lesson.

---

# 20. HTTP Authentication

HTTP may carry authentication information through headers.

Example:

```http
Authorization: Bearer eyJ...
```

Common schemes include:

```text
Basic
Bearer
Digest
```

Modern APIs frequently use bearer tokens.

### Security concern

If credentials or tokens are exposed, an attacker may potentially gain unauthorized access.

Therefore:

```text
HTTPS
+
Secure token handling
+
Proper authorization
```

are important.

---

# 21. HTTP Keep-Alive

Opening a new TCP connection for every request can be inefficient.

Connection reuse allows multiple requests/responses over an existing connection.

Conceptually:

```text
TCP Connection
      │
      ├── Request 1 → Response 1
      ├── Request 2 → Response 2
      ├── Request 3 → Response 3
      │
      └── Close
```

HTTP/1.1 commonly supports persistent connections.

HTTP/2 goes further with multiplexing.

---

# 22. HTTP/1.1 vs HTTP/2 vs HTTP/3

| Feature             | HTTP/1.1                | HTTP/2      | HTTP/3                  |
| ------------------- | ----------------------- | ----------- | ----------------------- |
| Transport           | TCP                     | TCP         | QUIC/UDP                |
| Multiplexing        | Limited                 | Yes         | Yes                     |
| Header compression  | No standard HPACK       | HPACK       | QPACK                   |
| Encryption required | No                      | No          | Yes in practice         |
| Main benefit        | Simple/widely supported | Performance | Faster modern transport |

Important:

> HTTP/3 does **not** mean "HTTP without TCP because UDP is more secure." QUIC provides transport features and TLS 1.3 integration over UDP.

---

# 23. HTTP Request/Response Full Mental Model

```text
              USER
               │
               ▼
          Web Browser
               │
               │ HTTP Request
               ▼
        ┌───────────────┐
        │ Web Server /  │
        │ Reverse Proxy │
        └───────┬───────┘
                │
                ▼
         Web Application
                │
        ┌───────┴────────┐
        ▼                ▼
    Database          Other API
        │                │
        └───────┬────────┘
                ▼
          HTTP Response
                │
                ▼
            Browser
```

---

# 24. What Happens in a Real Web Request?

Suppose:

```text
https://shop.example/products?id=10
```

The simplified flow is:

```text
DNS
 ↓
IP address

TCP/QUIC
 ↓
Connection

TLS
 ↓
Secure channel

HTTP
 ↓
GET /products?id=10

Web server
 ↓
Application
 ↓
Database

HTTP Response
 ↓
Browser
```

This is one of the most important mental models for Web VAPT.

---

# 25. HTTP Through the VAPT Lens

A pentester doesn't just ask:

> "Does the website work?"

They ask:

```text
Who sent the request?
        ↓
What resource is requested?
        ↓
What parameters are accepted?
        ↓
What authentication is required?
        ↓
What authorization is checked?
        ↓
What data is returned?
        ↓
What security controls exist?
```

---

# 26. HTTP Attack Surface

A web request may contain many testable components:

```text
URL
 ├── Scheme
 ├── Host
 ├── Port
 ├── Path
 └── Query parameters

Headers
 ├── Host
 ├── Cookie
 ├── Authorization
 ├── Origin
 └── User-Agent

Body
 ├── Form data
 ├── JSON
 ├── XML
 └── Multipart data
```

Each component can be relevant during authorized security testing.

---

# 27. Example: Parameter Testing

Request:

```http
GET /profile?id=100 HTTP/1.1
Host: lab.example
```

A tester may investigate whether the application properly enforces authorization around the object identified by:

```text
id=100
```

For example:

```text
User A → /profile?id=100
```

and another user's object:

```text
User A → /profile?id=101
```

If the application exposes another user's data without proper authorization, this may indicate an **IDOR/BOLA-style access-control flaw**.

The important concept is not simply changing the number; it is understanding whether the **server performs authorization checks**.

---

# 28. HTTP and XSS

Consider:

```http
GET /search?q=hello
```

The application receives:

```text
q=hello
```

A security tester checks whether user-controlled input is:

```text
Validated
Encoded
Sanitized
Safely rendered
```

Poor handling can contribute to XSS.

The vulnerability is caused by unsafe application behavior—not by HTTP itself.

---

# 29. HTTP and SQL Injection

Example:

```http
GET /products?id=10
```

The application may internally perform a database operation.

Conceptually:

```text
HTTP Parameter
      ↓
Application
      ↓
Database Query
      ↓
Database
```

If application input is improperly handled, injection vulnerabilities may occur.

Again:

> HTTP transports the input; the vulnerable component is generally the application's processing of that input.

---

# 30. HTTP Security Headers — What to Look For

During authorized testing, inspect responses for:

```text
Content-Security-Policy
Strict-Transport-Security
X-Content-Type-Options
Referrer-Policy
Permissions-Policy
Set-Cookie
Cache-Control
```

Example:

```http
Strict-Transport-Security: max-age=31536000
```

This tells browsers to prefer HTTPS for the specified policy period.

---

# 31. Useful Lab Tools

For learning HTTP safely:

### Browser Developer Tools

Use:

```text
F12 → Network
```

You can inspect:

```text
Request URL
Method
Status
Headers
Cookies
Request payload
Response
Timing
```

This is one of the best beginner tools.

### Burp Suite

Useful for authorized web application testing.

Conceptually:

```text
Browser
   ↓
Burp Proxy
   ↓
Web Application
```

It allows you to inspect and modify HTTP requests in a controlled lab.

---

# 32. Safe CLI Practice

For an authorized test system or public test target, you can inspect HTTP headers with:

```bash
curl -I https://example.com
```

A basic GET request:

```bash
curl https://example.com
```

Verbose connection information:

```bash
curl -v https://example.com
```

Use these only against systems you're permitted to test.

---

# 33. Python HTTP Example

Python's `requests` library can automate HTTP requests.

```python
import requests

url = "https://example.com"

response = requests.get(url, timeout=5)

print("Status:", response.status_code)
print("Content-Type:", response.headers.get("Content-Type"))
print("Length:", len(response.text))
```

Mental model:

```text
Python
  ↓
requests.get()
  ↓
HTTP Request
  ↓
Server
  ↓
HTTP Response
  ↓
response object
```

---

# 34. HTTP vs TCP — Don't Mix Them Up

This is a common beginner confusion.

```text
HTTP
 ↓
Application protocol

TCP
 ↓
Transport protocol

IP
 ↓
Internet protocol
```

Example:

```text
HTTP Request
     ↓
TCP Segment
     ↓
IP Packet
     ↓
Ethernet/Wi-Fi Frame
```

HTTP doesn't replace TCP.

---

# 35. Common Beginner Mistakes

### ❌ Mistake 1

"HTTP and TCP are the same."

### ✅ Correct

HTTP is an application protocol; TCP is a transport protocol.

---

### ❌ Mistake 2

"404 means the server is down."

### ✅ Correct

404 generally means the requested resource wasn't found.

---

### ❌ Mistake 3

"401 means I don't have permission."

### ✅ Better understanding

401 usually relates to authentication; 403 generally indicates refusal based on authorization/access control.

---

### ❌ Mistake 4

"HTTPS makes a website completely secure."

### ✅ Correct

HTTPS protects communication with TLS, but the application can still contain vulnerabilities.

---

### ❌ Mistake 5

"Every HTTP request has a body."

### ✅ Correct

A request body is optional and commonly used with methods such as POST, PUT and PATCH.

---

# 36. Interview Questions

### Q1. What is HTTP?

HTTP is an application-layer protocol used for communication between clients and servers.

### Q2. What is an HTTP request?

A message sent by a client to request a resource or perform an operation on a server.

### Q3. What is an HTTP response?

A message returned by the server after processing the request.

### Q4. What is a request header?

Metadata describing the request.

### Q5. What is a response header?

Metadata describing the server's response.

### Q6. Difference between HTTP and HTTPS?

HTTPS is HTTP transported through a TLS-protected connection.

### Q7. What does 200 mean?

Request succeeded.

### Q8. What does 404 mean?

Requested resource was not found.

### Q9. Difference between 401 and 403?

```text
401 → Authentication issue
403 → Authorization/access refusal
```

### Q10. What is Content-Type?

It indicates the media type of the message body.

### Q11. What is a cookie?

A piece of state information stored by the client and sent with applicable requests.

### Q12. What is HTTP statelessness?

HTTP does not inherently remember previous requests; applications add state mechanisms such as cookies and sessions.

---

# 37. Scenario-Based Questions

### Scenario 1

You send:

```http
GET /admin
```

and receive:

```text
403 Forbidden
```

What should you understand?

**Answer:** The server is refusing access; it does not automatically mean the endpoint doesn't exist.

---

### Scenario 2

You receive:

```http
200 OK
Content-Type: application/json
```

What does this tell you?

**Answer:** The request succeeded and the response body is identified as JSON.

---

### Scenario 3

A request contains:

```http
Cookie: session=...
```

What does it suggest?

**Answer:** The application may be using cookie-based session/state management.

---

### Scenario 4

A website uses HTTPS but has an authorization flaw.

Is HTTPS enough to prevent it?

**Answer:** No. TLS protects communication; authorization must be enforced by the application/server.

---

# 38. MCQs

### 1. HTTP operates primarily at which layer?

A. Physical
B. Data Link
C. Transport
D. Application

**Answer: D**

---

### 2. Which status code indicates success?

A. 200
B. 301
C. 403
D. 500

**Answer: A**

---

### 3. Which code usually indicates authentication is required/failed?

A. 200
B. 301
C. 401
D. 404

**Answer: C**

---

### 4. Which code generally represents forbidden access?

A. 201
B. 302
C. 403
D. 503

**Answer: C**

---

### 5. Which header identifies the requested host?

A. Host
B. Cookie
C. Accept
D. Referer

**Answer: A**

---

### 6. Which header can carry cookies from the browser?

A. Set-Cookie
B. Cookie
C. Host
D. Origin

**Answer: B**

---

### 7. Which response header tells the client the media type?

A. Host
B. Content-Type
C. Cookie
D. Origin

**Answer: B**

---

### 8. HTTPS primarily adds what to HTTP?

A. DNS
B. TLS protection
C. UDP
D. FTP

**Answer: B**

---

### 9. Which protocol is associated with HTTP/3?

A. TCP
B. UDP only
C. QUIC over UDP
D. ICMP

**Answer: C**

---

### 10. What is the purpose of a request body?

A. Always identify the IP
B. Carry application data
C. Perform DNS resolution
D. Establish Wi-Fi

**Answer: B**

---

### 11. Which tool is useful for viewing HTTP requests in a browser?

A. Task Manager
B. Developer Tools → Network
C. Calculator
D. Paint

**Answer: B**

---

### 12. Which command displays HTTP response headers?

A. `curl -I https://example.com`
B. `ping -I https://example.com`
C. `ssh -I https://example.com`
D. `ip -I https://example.com`

**Answer: A**

---

# 39. Practical Lab — HTTP Investigation

Use a system you own or an authorized training lab.

## Lab 1 — Browser

Open:

```text
Developer Tools
→ Network
```

Visit a test website.

Identify:

```text
Request method
URL
Status code
Request headers
Response headers
Content-Type
Cookies
Response body
```

---

## Lab 2 — curl

Run:

```bash
curl -I https://example.com
```

Record:

```text
Status
Server headers
Content-Type
Security headers
Caching headers
```

---

## Lab 3 — Python

Create:

```text
http_lab.py
```

Use:

```python
import requests

url = "https://example.com"

r = requests.get(url, timeout=5)

print("Status:", r.status_code)
print("Content-Type:", r.headers.get("Content-Type"))
print("Server:", r.headers.get("Server"))
print("Response length:", len(r.text))
```

Understand every line rather than simply copying it.

---

# 40. Advanced Concepts to Learn Next

After mastering this lesson, move toward:

```text
HTTP
 ↓
Cookies & Sessions
 ↓
Authentication
 ↓
Authorization
 ↓
REST APIs
 ↓
JSON
 ↓
CORS
 ↓
CSRF
 ↓
XSS
 ↓
SQL Injection
 ↓
HTTP Request Smuggling
 ↓
WebSockets
 ↓
HTTP/2 & HTTP/3
```

For your VAPT roadmap, the most important next concepts are:

**Cookies/Sessions → HTTP Methods/Status Codes → Headers → Authentication/Authorization → Burp Suite → OWASP Web vulnerabilities.**

---

# 41. Must-Memorize Cheat Sheet

```text
HTTP
= Application-layer protocol

HTTPS
= HTTP + TLS protection

80
= HTTP (default)

443
= HTTPS (default)

GET
= Retrieve/request resource

POST
= Submit/process data

200
= OK

201
= Created

301/302
= Redirect

400
= Bad Request

401
= Authentication required/failed

403
= Forbidden

404
= Not Found

429
= Too Many Requests

500
= Internal Server Error

502
= Bad Gateway

503
= Service Unavailable
```

### Request

```text
Method
Path
Version
Headers
Body
```

### Response

```text
Version
Status
Headers
Body
```

### Security

```text
Cookie
Session
Authorization
CSP
HSTS
CORS
CSRF
XSS
Access Control
```

---

# 42. Final Mental Model

Don't memorize HTTP as random commands.

Think:

```text
USER
 │
 ▼
BROWSER
 │
 │  HTTP REQUEST
 │
 │  Method
 │  URL
 │  Headers
 │  Cookies
 │  Body
 ▼
WEB SERVER
 │
 ▼
APPLICATION
 │
 ├── Authentication
 ├── Authorization
 ├── Input validation
 ├── Business logic
 └── Database
 │
 ▼
HTTP RESPONSE
 │
 │ Status
 │ Headers
 │ Cookies
 │ Body
 ▼
BROWSER
```

For **Web VAPT**, your core question becomes:

> **"What does the client send, what does the server trust, what does the server return, and what security controls are enforced between those two points?"**

That mental model will make later topics like **Burp Suite, authentication, IDOR/BOLA, XSS, SQLi, CSRF, CORS and API testing** much easier.

