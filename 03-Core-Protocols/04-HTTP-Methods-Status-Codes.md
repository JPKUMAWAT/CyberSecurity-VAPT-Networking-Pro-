

# 1. What is HTTP?

**HTTP (HyperText Transfer Protocol)** is an application-layer protocol used for communication between clients and web servers.

Typical flow:

```text
Browser
   │
   │ HTTP Request
   ▼
Web Server
   │
   │ HTTP Response
   ▼
Browser
```

Example:

```text
GET /login HTTP/1.1
Host: example.com
```

Server might respond:

```text
HTTP/1.1 200 OK
```

HTTP itself is generally **stateless**: each request is independent unless mechanisms such as cookies, sessions, or tokens connect requests to a user's session.

---

# 2. What is an HTTP Method?

An HTTP method tells the server **what operation the client wants to perform**.

Think of it as an instruction:

```text
GET     → Give me this resource
POST    → Process/create something using this data
PUT     → Replace this resource
PATCH   → Modify part of this resource
DELETE  → Delete this resource
HEAD    → Give me headers without the normal response body
OPTIONS → Tell me which methods/options are available
```

---

# 3. Most Important HTTP Methods

| Method  | Main purpose               | Usually has body? | Typical use             |
| ------- | -------------------------- | ----------------: | ----------------------- |
| GET     | Retrieve resource          |               No* | View page/data          |
| POST    | Submit/process data        |               Yes | Login/form/API creation |
| PUT     | Replace resource           |               Yes | Replace API object      |
| PATCH   | Partially modify           |               Yes | Update one field        |
| DELETE  | Delete resource            |         Sometimes | Delete API object       |
| HEAD    | Headers only               |                No | Check resource metadata |
| OPTIONS | Discover supported methods |        Usually no | Capability/CORS checks  |

*A GET request can technically contain a body, but servers/proxies should not generally rely on it.

---

# 4. GET

## Definition

`GET` requests a representation of a resource.

Example:

```http
GET /products/10 HTTP/1.1
Host: example.com
```

The server may return:

```http
HTTP/1.1 200 OK
Content-Type: application/json
```

```json
{
  "id": 10,
  "name": "Laptop"
}
```

### VAPT relevance

During authorized testing, GET requests are commonly inspected for:

* Exposed parameters
* IDOR/BOLA possibilities
* Information disclosure
* Authentication issues
* Reflected input
* Cache-related problems

Example:

```text
GET /profile?id=100
```

A tester may investigate whether changing the identifier is properly authorized.

> The security issue isn't the GET method itself; it's usually improper authorization or input handling around the resource.

---

# 5. POST

## Definition

`POST` submits data to the server for processing.

Example:

```http
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

username=alice&password=example
```

Another common API example:

```http
POST /api/users HTTP/1.1
Content-Type: application/json

{
  "username": "alice"
}
```

### Common uses

* Login
* Registration
* Form submission
* Creating resources
* Uploading data
* API operations

### VAPT relevance

Testers examine:

* Authentication
* Authorization
* Input validation
* CSRF protections where applicable
* Rate limiting
* Injection defenses
* File upload controls

---

# 6. PUT

`PUT` generally means:

> Replace the current representation of a resource with the supplied representation.

Example:

```http
PUT /api/users/10 HTTP/1.1
Content-Type: application/json

{
  "name": "Alice",
  "role": "user"
}
```

A common misconception:

> PUT does not automatically mean "update anything I want."

The server must still enforce authorization.

### VAPT relevance

If an application incorrectly allows a normal user to modify another user's object:

```text
PUT /api/users/10
```

that could indicate an authorization vulnerability such as **BOLA/IDOR**.

---

# 7. PATCH

`PATCH` is used for **partial modification**.

Example:

```http
PATCH /api/users/10 HTTP/1.1
Content-Type: application/json

{
  "email": "new@example.com"
}
```

Only the specified field is intended to change.

### PUT vs PATCH

```text
PUT
 ↓
Replace resource

PATCH
 ↓
Modify part of resource
```

Example:

Current:

```json
{
  "name": "Alice",
  "email": "old@example.com",
  "role": "user"
}
```

PATCH:

```json
{
  "email": "new@example.com"
}
```

Conceptually:

```text
name  → unchanged
email → changed
role  → unchanged
```

---

# 8. DELETE

`DELETE` requests removal of a resource.

Example:

```http
DELETE /api/users/10 HTTP/1.1
Host: example.com
```

Possible response:

```http
HTTP/1.1 204 No Content
```

### VAPT relevance

Check whether authorization is correctly enforced.

For example:

```text
Normal user
     │
     ▼
DELETE /api/users/another-user
     │
     ▼
Should → 403/404 according to application design
```

If unauthorized deletion succeeds, it can be a serious authorization flaw.

---

# 9. HEAD

`HEAD` is similar to `GET`, but the server normally returns **headers without the response body**.

Example:

```http
HEAD / HTTP/1.1
Host: example.com
```

Possible response:

```http
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 5421
```

### Why useful?

Useful for checking metadata without downloading the complete resource.

### VAPT relevance

Can help inspect:

* Server behavior
* Headers
* Content type
* Resource availability
* Caching behavior

---

# 10. OPTIONS

`OPTIONS` asks about communication options supported by a resource/server.

Example:

```http
OPTIONS /api/ HTTP/1.1
Host: example.com
```

Possible response:

```http
HTTP/1.1 204 No Content
Allow: GET, POST, OPTIONS
```

### VAPT relevance

It can provide useful information during reconnaissance.

However:

> `OPTIONS` showing a method does **not** automatically mean that method is exploitable.

Authorization and application behavior still matter.

---

# 11. Safe, Idempotent & Cacheable Concepts

These are important for interviews.

## Safe Methods

A method is considered **safe** when it is intended not to change server state.

Common safe methods:

```text
GET
HEAD
OPTIONS
```

---

## Idempotent Methods

A method is idempotent when repeating the same request has the same intended effect on server state as making it once.

Commonly idempotent:

```text
GET
HEAD
PUT
DELETE
OPTIONS
```

### Important distinction

**Idempotent ≠ safe**

For example:

```text
DELETE
```

is generally idempotent but **not safe**, because it changes server state.

---

# 12. HTTP Status Codes

Every HTTP response normally contains a status code.

Example:

```http
HTTP/1.1 200 OK
```

The number tells the broad result of the request.

There are five classes:

```text
1xx → Informational
2xx → Success
3xx → Redirection
4xx → Client-side/request problem
5xx → Server-side failure
```

---

# 13. 1xx — Informational

These indicate that the request is being processed or additional protocol steps are involved.

Examples:

```text
100 Continue
101 Switching Protocols
103 Early Hints
```

For beginner VAPT work, 1xx codes are less frequently encountered than 2xx–5xx.

---

# 14. 2xx — Success

The request was successfully processed.

## 200 OK

Most famous status code.

```http
HTTP/1.1 200 OK
```

Means the request succeeded.

Common with:

```text
GET / HTTP/1.1
```

---

## 201 Created

A new resource was created.

Example:

```http
POST /api/users
```

Response:

```http
HTTP/1.1 201 Created
Location: /api/users/101
```

Common in REST APIs.

---

## 202 Accepted

The request was accepted for processing, but processing may not have finished.

Useful for asynchronous operations.

---

## 204 No Content

Request succeeded but there is no response body.

Common with:

```text
DELETE
PATCH
PUT
```

depending on application design.

---

# 15. 3xx — Redirection

The client needs to follow a redirect or use another representation/location.

## 301 Moved Permanently

Resource has been permanently moved.

Example:

```http
HTTP/1.1 301 Moved Permanently
Location: https://example.com/
```

---

## 302 Found

Temporary redirection in common modern usage.

Example:

```http
HTTP/1.1 302 Found
Location: /login
```

---

## 303 See Other

Often used after an operation to tell the client to retrieve another resource using GET.

---

## 304 Not Modified

Used with caching.

It tells the client that its cached representation can still be used.

---

## 307 Temporary Redirect

Temporary redirect while preserving the HTTP method.

---

## 308 Permanent Redirect

Permanent redirect while preserving the HTTP method.

---

# 16. 301/302 vs 307/308

This is a useful interview topic.

Traditional:

```text
301 / 302
```

Historically had method-changing behavior in some clients.

More explicit method-preserving redirects:

```text
307
308
```

Remember:

```text
307 → Temporary + preserve method
308 → Permanent + preserve method
```

---

# 17. 4xx — Client/Error Request Side

These indicate that the request cannot be fulfilled because of a problem associated with the request.

## 400 Bad Request

Server cannot process the request because it is malformed/invalid.

Example:

```http
POST /api/login
Content-Type: application/json

{"username":
```

Invalid JSON could produce:

```text
400 Bad Request
```

---

# 18. 401 Unauthorized

This status usually means:

> Authentication is required or authentication credentials are invalid.

Example:

```http
GET /api/profile
```

Response:

```http
401 Unauthorized
```

### Important

Many beginners remember:

```text
401 = permission denied
```

Better:

```text
401 → Authentication problem
403 → Authorization problem
```

---

# 19. 403 Forbidden

The server understood the request but refuses to authorize it.

Example:

```text
Authenticated user
       ↓
Requests admin resource
       ↓
403 Forbidden
```

### VAPT relevance

Very important for authorization testing.

---

# 20. 404 Not Found

The server could not find the requested resource.

Example:

```http
GET /does-not-exist
```

Response:

```text
404 Not Found
```

### Security relevance

Different 404 behaviors can sometimes reveal information.

For example:

```text
Existing object → 403
Non-existing object → 404
```

This can sometimes help testers understand authorization and resource existence behavior.

---

# 21. 405 Method Not Allowed

The resource exists, but that HTTP method is not allowed.

Example:

```http
DELETE /profile
```

Response:

```text
405 Method Not Allowed
Allow: GET, POST
```

This is particularly useful when studying API behavior.

---

# 22. 408 Request Timeout

Server timed out waiting for the request.

---

# 23. 409 Conflict

Request conflicts with the current state of the resource.

Example:

```text
Trying to create an account that already exists
```

---

# 24. 429 Too Many Requests ⭐

The client sent too many requests within a given period.

Example:

```text
Repeated API requests
        ↓
Rate limit
        ↓
429 Too Many Requests
```

### VAPT relevance

Very important when evaluating:

* Rate limiting
* Login protection
* API abuse controls
* Automated testing behavior

---

# 25. 5xx — Server Errors

These indicate that the server failed to fulfill an otherwise valid request.

---

## 500 Internal Server Error

Generic server-side failure.

Example:

```text
Application exception
        ↓
500 Internal Server Error
```

### VAPT relevance

Unexpected 500 responses may sometimes reveal:

* Debug information
* Stack traces
* Framework details
* Application errors

But a 500 alone does **not** prove a vulnerability.

---

# 26. 501 Not Implemented

Server does not support the functionality required to fulfill the request.

---

# 27. 502 Bad Gateway

A gateway/proxy received an invalid response from an upstream server.

Architecture:

```text
Client
  ↓
Reverse Proxy
  ↓
Application Server
```

If the proxy gets a bad upstream response:

```text
502
```

---

# 28. 503 Service Unavailable

Server is currently unable to handle the request.

Possible reasons:

* Maintenance
* Overload
* Temporary failure

---

# 29. 504 Gateway Timeout

Gateway/proxy did not receive a timely response from the upstream server.

```text
Client
  ↓
Proxy
  ↓
Backend
  X
  └── timeout
```

Result:

```text
504 Gateway Timeout
```

---

# 30. Most Important Status Codes to Memorize

| Code | Meaning                    | Priority |
| ---: | -------------------------- | -------- |
|  200 | OK                         | ⭐⭐⭐⭐⭐    |
|  201 | Created                    | ⭐⭐⭐⭐     |
|  204 | No Content                 | ⭐⭐⭐      |
|  301 | Permanent Redirect         | ⭐⭐⭐      |
|  302 | Temporary/Common Redirect  | ⭐⭐⭐⭐     |
|  304 | Not Modified               | ⭐⭐⭐      |
|  400 | Bad Request                | ⭐⭐⭐⭐⭐    |
|  401 | Unauthorized/Auth required | ⭐⭐⭐⭐⭐    |
|  403 | Forbidden                  | ⭐⭐⭐⭐⭐    |
|  404 | Not Found                  | ⭐⭐⭐⭐⭐    |
|  405 | Method Not Allowed         | ⭐⭐⭐⭐     |
|  409 | Conflict                   | ⭐⭐⭐      |
|  429 | Too Many Requests          | ⭐⭐⭐⭐⭐    |
|  500 | Internal Server Error      | ⭐⭐⭐⭐⭐    |
|  502 | Bad Gateway                | ⭐⭐⭐      |
|  503 | Service Unavailable        | ⭐⭐⭐      |
|  504 | Gateway Timeout            | ⭐⭐⭐      |

---

# 31. 401 vs 403 — MUST KNOW

```text
401
 ↓
Who are you?
 ↓
Authentication problem
```

```text
403
 ↓
I know who you are,
but you're not allowed.
 ↓
Authorization problem
```

Example:

```text
No valid login
    ↓
401
```

```text
Logged-in normal user
    ↓
Attempts admin-only action
    ↓
403
```

---

# 32. HTTP Method + Status Code Together

Don't analyze methods and status codes separately.

Example:

```http
DELETE /api/users/20
```

Possible responses:

```text
204 → deletion succeeded
401 → authentication required
403 → authenticated but not authorized
404 → resource not found
405 → DELETE isn't supported
409 → state conflict
500 → server-side failure
```

This is how a pentester starts understanding application behavior.

---

# 33. VAPT Method Testing Mindset

In an authorized lab, don't simply ask:

> "Is POST allowed?"

Ask:

```text
What resource?
       ↓
Which HTTP method?
       ↓
Who can perform it?
       ↓
What authentication is required?
       ↓
What authorization is enforced?
       ↓
What input is accepted?
       ↓
What response is returned?
       ↓
Does behavior change for different users?
```

This is much more valuable than memorizing status codes.

---

# 34. Example API Analysis

Suppose an application has:

```text
GET    /api/profile/10
PATCH  /api/profile/10
DELETE /api/profile/10
```

A tester might document:

| Request | Expected             | Actual              |
| ------- | -------------------- | ------------------- |
| GET     | Authorized user only | 200                 |
| PATCH   | Owner/admin only     | 403 for normal user |
| DELETE  | Owner/admin only     | 403 for normal user |

This provides useful evidence about authorization controls.

---

# 35. Testing Methods with curl

Use only against systems you own or are explicitly authorized to test.

### GET

```bash
curl -i http://localhost:8000/
```

### HEAD

```bash
curl -I http://localhost:8000/
```

### OPTIONS

```bash
curl -i -X OPTIONS http://localhost:8000/
```

### POST

```bash
curl -i -X POST http://localhost:8000/login
```

The `-i` option displays response headers along with the response.

---

# 36. Testing with Burp Suite

A typical authorized web-testing workflow:

```text
Browser
   ↓
Burp Proxy
   ↓
Application
```

You can inspect:

```text
Method
URL
Headers
Cookies
Parameters
Body
Status Code
Response Headers
Response Body
```

Example:

```http
GET /api/profile/10 HTTP/1.1
Host: lab.local
Cookie: session=...
```

Response:

```http
HTTP/1.1 200 OK
Content-Type: application/json
```

This lets you understand exactly how the application behaves.

---

# 37. Common Beginner Mistakes

### ❌ Mistake 1

Thinking:

```text
401 = Authorization failure
```

Better:

```text
401 → Authentication
403 → Authorization
```

---

### ❌ Mistake 2

Thinking:

```text
200 = Website is secure
```

Wrong.

A vulnerable application can return:

```text
200 OK
```

---

### ❌ Mistake 3

Thinking:

```text
404 = definitely no resource
```

Not necessarily.

Applications may intentionally return generic responses.

---

### ❌ Mistake 4

Thinking:

```text
405 = vulnerability
```

No.

It simply means the requested method isn't allowed for that resource.

---

### ❌ Mistake 5

Thinking:

```text
OPTIONS shows DELETE → DELETE is exploitable
```

Not necessarily.

You still need to understand:

```text
Authentication
Authorization
Application logic
```

---

# 38. Interview Questions

### Q1. What is an HTTP method?

A method indicates the intended operation for an HTTP request.

### Q2. What does GET do?

Retrieves a representation of a resource.

### Q3. GET vs POST?

```text
GET  → retrieve
POST → submit/process/create
```

### Q4. PUT vs PATCH?

```text
PUT   → generally replace
PATCH → partially modify
```

### Q5. What does 200 mean?

Request succeeded.

### Q6. 401 vs 403?

```text
401 → authentication issue
403 → authorization/permission issue
```

### Q7. What does 404 mean?

Requested resource could not be found.

### Q8. What does 405 mean?

Method isn't allowed for that resource.

### Q9. What does 429 indicate?

Too many requests/rate limiting.

### Q10. What does 500 indicate?

Generic server-side error.

### Q11. What does 502 mean?

Gateway/proxy received an invalid response from an upstream server.

### Q12. What does 504 mean?

Gateway/proxy timed out waiting for an upstream response.

---

# 39. Scenario-Based Questions

### Scenario 1

You access:

```text
GET /admin
```

and receive:

```text
403 Forbidden
```

What does this primarily tell you?

**Answer:** The server is refusing access; authorization/access control is likely involved.

---

### Scenario 2

You send a request without valid authentication and receive:

```text
401
```

What should you investigate?

**Answer:** Authentication/session requirements.

---

### Scenario 3

An API returns:

```text
429
```

after many requests.

What security control might be present?

**Answer:** Rate limiting.

---

### Scenario 4

An application returns:

```text
500
```

when unexpected input is supplied.

Is that automatically a vulnerability?

**Answer:** No. Investigate the error behavior and whether sensitive information or exploitable behavior is exposed.

---

# 40. MCQs

### 1. Which method is primarily used to retrieve a resource?

A. POST
B. GET
C. DELETE
D. PATCH

**Answer: B**

---

### 2. Which method is commonly used to create a resource in REST APIs?

A. POST
B. HEAD
C. OPTIONS
D. GET

**Answer: A**

---

### 3. Which status code means "OK"?

A. 201
B. 301
C. 200
D. 404

**Answer: C**

---

### 4. Which status code is commonly associated with authentication problems?

A. 401
B. 403
C. 404
D. 500

**Answer: A**

---

### 5. Which status code represents forbidden access?

A. 200
B. 302
C. 403
D. 503

**Answer: C**

---

### 6. Which status code means "Not Found"?

A. 400
B. 404
C. 405
D. 500

**Answer: B**

---

### 7. Which status code commonly indicates rate limiting?

A. 201
B. 304
C. 429
D. 502

**Answer: C**

---

### 8. Which method is designed to return headers without the normal response body?

A. HEAD
B. POST
C. PUT
D. DELETE

**Answer: A**

---

### 9. Which method generally replaces a resource representation?

A. PATCH
B. PUT
C. GET
D. HEAD

**Answer: B**

---

### 10. Which status code represents a server-side internal error?

A. 400
B. 401
C. 404
D. 500

**Answer: D**

---

### 11. Which is a 3xx status code?

A. 201
B. 301
C. 401
D. 500

**Answer: B**

---

### 12. Which status code indicates that a method is not allowed?

A. 403
B. 404
C. 405
D. 409

**Answer: C**

---

### 13. Which method is commonly used for partial updates?

A. PATCH
B. GET
C. HEAD
D. OPTIONS

**Answer: A**

---

### 14. What does 204 mean?

A. Unauthorized
B. No Content
C. Not Found
D. Server Error

**Answer: B**

---

### 15. What does 503 generally indicate?

A. Service unavailable
B. Authentication required
C. Resource created
D. Redirect

**Answer: A**

---

# 41. Must-Memorize Cheat Sheet

## Methods

```text
GET      → Retrieve
POST     → Submit/Create/Process
PUT      → Replace
PATCH    → Partial update
DELETE   → Delete
HEAD     → Headers only
OPTIONS  → Supported options/methods
```

## Status Codes

```text
1xx → Information
2xx → Success
3xx → Redirect
4xx → Request/client-side issue
5xx → Server-side issue
```

### Most important:

```text
200 → OK
201 → Created
204 → No Content

301 → Permanent Redirect
302 → Redirect
304 → Not Modified

400 → Bad Request
401 → Authentication
403 → Forbidden/Authorization
404 → Not Found
405 → Method Not Allowed
429 → Too Many Requests

500 → Internal Server Error
502 → Bad Gateway
503 → Service Unavailable
504 → Gateway Timeout
```

---

# 42. Final Mental Model

When you see an HTTP request:

```text
METHOD + URL
      ↓
Authentication?
      ↓
Authorization?
      ↓
Input?
      ↓
Server processing
      ↓
STATUS CODE
      ↓
RESPONSE
```

For VAPT, think:

```text
HTTP Method
     +
Authentication
     +
Authorization
     +
Input Validation
     +
Application Logic
     +
Response Behavior
     =
Security Assessment
```

**Don't just memorize `GET = 200` or `POST = 201`.** Real applications can return many different status codes depending on authentication, authorization, validation, and business logic.

---

# Key Takeaways

* HTTP methods describe the intended operation.
* GET retrieves resources; POST commonly submits/creates data.
* PUT generally replaces; PATCH partially modifies.
* DELETE requests resource deletion.
* HEAD retrieves headers without the normal response body.
* OPTIONS provides information about supported communication options.
* `2xx` generally means success.
* `3xx` generally means redirection.
* `4xx` generally indicates a request/client-side issue.
* `5xx` generally indicates a server-side failure.
* **401 = authentication**, while **403 = authorization/access denial**.
* `429` is important when analyzing rate limiting.
* A status code alone does **not** prove a vulnerability.

## Practical Lab Ideas

1. Build a small local HTTP server and observe GET/POST responses.
2. Use `curl -i` to inspect methods and status codes.
3. Use Burp Suite against an intentionally vulnerable/local lab application.
4. Create a table of each endpoint, supported methods, authentication requirement, and returned status.
5. Compare how `401`, `403`, `404`, and `405` behave in a local test application.

## Recommended Learning Progression

```text
HTTP Basics
    ↓
Methods + Status Codes  ← YOU ARE HERE
    ↓
Headers
    ↓
Cookies & Sessions
    ↓
Authentication
    ↓
Authorization
    ↓
APIs
    ↓
Burp Suite
    ↓
OWASP Web Vulnerabilities
```

