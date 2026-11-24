Absolutely. If your goal is **“I want to look at a security question/code snippet and immediately recognize what kind of attack it is”**, you should learn security as a **taxonomy**, not as a random list of vulnerabilities.

Below is a broad **Security Vulnerability Master Map** covering the major web, API, cloud, application, container, database, authentication, and distributed-system attacks a senior/staff engineer should recognize.

---

# 🔐 Security Vulnerability Master Guide

The easiest mental model is:

```text
                    ATTACKER
                       │
                       ▼
                ┌─────────────┐
                │   INPUT     │
                └──────┬──────┘
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       Browser       API          Server
          │            │            │
          ▼            ▼            ▼
        HTML          DB          OS
        DOM           Cache       Files
        Cookie        Queue       Network
          │            │            │
          └────────────┼────────────┘
                       ▼
                APPLICATION
```

Almost every vulnerability can be understood by asking:

> **What does the attacker control, where does that data go, and what security boundary does it cross?**

---

# 1. XSS — Cross-Site Scripting ⭐⭐⭐⭐⭐

### Attack

Attacker-controlled data becomes executable content in a browser.

```text
Attacker input
     ↓
Web application
     ↓
HTML/JavaScript
     ↓
Victim's browser
```

### Types

* Reflected XSS
* Stored XSS
* DOM-based XSS
* Mutation XSS

### Common causes

```text
innerHTML
unsafe HTML rendering
unescaped templates
unsafe DOM manipulation
```

### Prevention

* Context-aware output encoding
* Safe templating
* Avoid unsafe HTML APIs
* Content Security Policy
* Input validation as defense-in-depth
* `HttpOnly` cookies for session tokens

---

# 2. SQL Injection ⭐⭐⭐⭐⭐

### Attack

User input changes the meaning of a database query.

```text
User input
    ↓
SQL string construction
    ↓
Database interprets attacker-controlled SQL
```

### Prevention

```text
Parameterized queries
Prepared statements
ORM parameter binding
Least-privilege DB accounts
```

**Golden rule:**

> Never concatenate untrusted input into SQL.

---

# 3. NoSQL Injection

Same basic concept as SQL injection, but against databases such as MongoDB or other query systems.

```text
User input
    ↓
Database query object
    ↓
Query behavior manipulated
```

### Prevention

* Strict schemas
* Typed request models
* Allowlisted query fields/operators
* Parameterized APIs
* Don't blindly convert user JSON into database queries

---

# 4. Command Injection ⭐⭐⭐⭐⭐

Attacker-controlled data reaches an operating-system command.

```text
HTTP request
    ↓
Application
    ↓
Shell / OS command
    ↓
Operating system
```

### Prevention

Best:

> Avoid shell commands entirely; use a library/API.

Otherwise:

* Strict allowlists
* Safe argument APIs
* No shell interpretation
* Least-privileged service account

---

# 5. LDAP Injection

Input manipulates an LDAP query.

```text
User input
    ↓
LDAP query
    ↓
Directory service
```

### Prevention

* LDAP-specific escaping
* Parameterized LDAP APIs
* Input validation

---

# 6. XPath Injection

Same concept, but attacker manipulates XPath expressions.

```text
Input → XPath → XML data
```

### Prevention

Parameterized XPath where supported and strict input validation.

---

# 7. Expression Language / Template Injection

Some frameworks evaluate expressions supplied through templates.

```text
User input
     ↓
Template engine
     ↓
Expression evaluation
```

Potential impact can range from data disclosure to code execution depending on the engine.

### Prevention

* Never evaluate user input as code
* Sandboxed template engines
* Safe template APIs
* Strict separation between data and templates

---

# 8. SSRF — Server-Side Request Forgery ⭐⭐⭐⭐⭐

Your first assessment question.

```text
Attacker
   │
   ▼
Application
   │
   ▼
Attacker-controlled URL
   │
   ▼
Internal/external resource
```

The important distinction:

> **The SERVER makes the request.**

### Prevention

* URL allowlists
* Restrict outbound traffic
* Network segmentation
* Block internal/private destinations
* Validate redirects
* Secure cloud metadata access

---

# 9. CSRF — Cross-Site Request Forgery ⭐⭐⭐⭐⭐

Here:

> **The victim's browser makes the request.**

```text
Attacker website
       │
       ▼
Victim browser
       │
       ▼
Target application
```

### Prevention

* CSRF tokens
* `SameSite` cookies
* Origin validation
* Proper authentication design

---

# 10. IDOR / BOLA ⭐⭐⭐⭐⭐

**Insecure Direct Object Reference / Broken Object Level Authorization**

Example:

```text
GET /orders/123
```

User changes:

```text
123 → 124
```

and sees someone else's order.

### Prevention

Every object access must perform authorization.

```text
Can currentUser access objectId?
              │
          YES │ NO
              ▼
           return
```

---

# 11. Broken Function-Level Authorization

User shouldn't be allowed to perform an operation but can invoke its API.

For example:

```text
Regular user
     ↓
/admin/deleteUser
```

### Prevention

Authorization must be enforced **server-side on every protected operation**.

Never rely on:

```text
hidden button
frontend role
disabled UI
```

---

# 12. Privilege Escalation ⭐⭐⭐⭐⭐

Two forms:

### Vertical

```text
USER
 ↓
ADMIN
```

### Horizontal

```text
USER A
 ↓
USER B's resources
```

IDOR is often a form of horizontal authorization failure.

### Prevention

* RBAC
* ABAC
* Server-side authorization
* Least privilege
* Privilege checks at service boundaries

---

# 13. Broken Authentication ⭐⭐⭐⭐⭐

Problems with proving identity.

Examples:

* Weak authentication
* Credential stuffing
* Brute force
* Poor password recovery
* Account enumeration
* Missing MFA
* Weak authentication flows

### Prevention

* MFA
* Strong authentication
* Rate limiting
* Secure password hashing
* Secure account recovery
* Monitoring

---

# 14. Session Hijacking

Attacker obtains a valid session identifier.

```text
Victim
   ↓
Session token
   ↓
Attacker obtains token
   ↓
Attacker impersonates victim
```

### Prevention

```text
Secure
HttpOnly
SameSite
```

cookies, HTTPS, session rotation, short appropriate lifetimes, and session invalidation.

---

# 15. Session Fixation

Attacker causes a victim to authenticate using a session identifier already known to the attacker.

### Prevention

> Generate/rotate the session ID after authentication.

---

# 16. Credential Stuffing

Attacker uses username/password combinations leaked from another service.

```text
Company A breach
       ↓
Leaked credentials
       ↓
Try against Company B
```

### Prevention

* MFA
* Credential breach detection
* Rate limiting
* Bot detection
* Strong authentication

---

# 17. Brute Force

Repeatedly guessing credentials.

### Prevention

* Rate limiting
* MFA
* Account protection
* Progressive delays
* Detection/alerting

---

# 18. Password Spraying

Instead of trying many passwords against one account:

```text
Password123
     ↓
many accounts
```

This avoids some account-lockout mechanisms.

### Prevention

* MFA
* Identity-provider protections
* Anomaly detection
* Rate limiting

---

# 19. Security Misconfiguration ⭐⭐⭐⭐⭐

Your Question #5.

Examples:

```text
Debug enabled
Default credentials
Public database
Exposed admin endpoint
Directory listing
Sensitive files accessible
Overly permissive CORS
Verbose errors
Unused ports
```

### Prevention

* Secure defaults
* Infrastructure-as-code
* Configuration scanning
* Automated security checks
* Production hardening
* Remove unnecessary components

---

# 20. Information Disclosure

Application reveals information it shouldn't.

Examples:

```text
Stack traces
Database names
Internal IPs
Cloud infrastructure
API keys
User information
Version information
```

### Prevention

```text
Client → generic error
Server → detailed secure logs
```

---

# 21. Path Traversal ⭐⭐⭐⭐⭐

Attacker manipulates a file path.

```text
Application
    ↓
User-controlled filename
    ↓
Filesystem
```

Potentially accesses files outside the intended directory.

### Prevention

* Canonicalize paths
* Allowlist filenames
* Map IDs to files
* Restrict filesystem permissions
* Store files outside sensitive directories

---

# 22. Local File Inclusion — LFI

Application includes a local file based on attacker-controlled input.

```text
Input
 ↓
File include
 ↓
Local filesystem
```

### Prevention

* Don't dynamically include arbitrary paths
* Allowlist resources
* Secure file mapping

---

# 23. Remote File Inclusion — RFI

Application loads a remote resource as code/content.

Conceptually:

```text
Attacker-controlled location
        ↓
Application
        ↓
Remote resource
```

Modern frameworks often mitigate this, but the underlying class remains important.

---

# 24. Unrestricted File Upload ⭐⭐⭐⭐⭐

Application allows dangerous or unexpected files.

Potential problems:

```text
Executable content
Malicious scripts
Oversized files
Malicious documents
Polyglot files
```

### Prevention

* Allowlisted file types
* Verify actual file content
* Size limits
* Randomized server filenames
* Non-executable storage
* Malware scanning where appropriate

---

# 25. XML External Entity — XXE

An XML parser processes external entities supplied through attacker-controlled XML.

```text
Attacker XML
     ↓
XML parser
     ↓
External resource/file
```

Can lead to data disclosure or SSRF depending on parser configuration.

### Prevention

> Disable external entity processing and unsafe DTD features.

Use hardened XML parsers.

---

# 26. Deserialization Vulnerabilities ⭐⭐⭐⭐⭐

Application accepts serialized objects from an untrusted source.

```text
Untrusted serialized data
          ↓
Deserializer
          ↓
Object creation / execution
```

Depending on technology, this can lead to:

* Code execution
* Data manipulation
* Authentication bypass
* DoS

### Prevention

* Don't deserialize untrusted objects
* Use simple data formats
* Allowlist types
* Integrity/signature validation
* Harden serializers

---

# 27. Prototype Pollution — JavaScript

Attacker manipulates JavaScript object prototypes.

```text
Attacker input
      ↓
Object merge
      ↓
Prototype
      ↓
Unexpected application behavior
```

### Prevention

* Safe object merging
* Validate keys
* Avoid unsafe deep merge utilities
* Use safer data structures where appropriate

---

# 28. Open Redirect

Application redirects users based on attacker-controlled URLs.

```text
Your trusted site
      ↓
redirect
      ↓
Attacker site
```

### Why dangerous?

Can facilitate:

* Phishing
* OAuth abuse
* Trust exploitation

### Prevention

Use allowlisted destinations or server-side destination IDs.

---

# 29. CORS Misconfiguration

Cross-Origin Resource Sharing controls which origins can access browser resources.

Dangerous configurations can expose sensitive APIs to unintended origins.

### Prevention

* Explicit origin allowlist
* Don't blindly reflect `Origin`
* Don't combine credentials with overly broad origins
* Review CORS per API

---

# 30. Clickjacking

Attacker embeds your site inside another webpage and tricks users into clicking something.

### Prevention

Use:

```text
Content-Security-Policy: frame-ancestors
```

and appropriate frame protections.

---

# 31. HTTP Request Smuggling ⭐⭐⭐⭐

Occurs when different components interpret HTTP request boundaries differently.

Typical architecture:

```text
Client
  ↓
Proxy / Load Balancer
  ↓
Web Server
```

If they disagree about request parsing, an attacker may manipulate request handling.

### Prevention

* Consistent HTTP parsing
* Hardened/properly configured proxies
* Updated infrastructure
* Reject ambiguous requests

---

# 32. HTTP Response Splitting

Attacker manipulates HTTP headers through unsafe user input.

### Prevention

* Never place raw user input into HTTP headers
* Proper header encoding
* Framework-provided response APIs

---

# 33. Host Header Injection

Application trusts an attacker-controlled HTTP `Host` header.

Can affect:

* Password-reset URLs
* Absolute URLs
* Routing
* Cache behavior

### Prevention

Use trusted configured hostnames rather than blindly trusting request headers.

---

# 34. Cache Poisoning

Attacker causes a shared cache to store malicious or incorrect content.

```text
Attacker request
      ↓
Application
      ↓
Cache
      ↓
Victims
```

### Prevention

* Correct cache keys
* Normalize requests
* Don't cache sensitive responses
* Validate cache-related headers

---

# 35. Cache Deception

Attacker tricks a cache into storing content that should not be publicly cached.

### Prevention

Careful cache-control policies and correct routing/cache-key design.

---

# 36. Race Condition ⭐⭐⭐⭐⭐

Two requests exploit timing.

```text
Request A ─────┐
               ├── shared state
Request B ─────┘
```

Examples:

* Double spending
* Duplicate order creation
* Inventory overselling
* Coupon reuse

### Prevention

* Atomic operations
* Database transactions
* Locks
* Optimistic concurrency
* Idempotency keys

This one is especially important for **OMS/inventory/payment systems**.

---

# 37. TOCTOU

**Time-of-check to time-of-use**

```text
Check permission
       ↓
      time
       ↓
Use resource
```

Resource changes between the two operations.

### Prevention

Make security-sensitive checks and operations atomic where possible.

---

# 38. Business Logic Abuse ⭐⭐⭐⭐⭐

No traditional injection is required.

The attacker simply abuses legitimate functionality.

Example:

```text
Coupon:
Once per customer

Attacker:
finds workflow allowing repeated redemption
```

### Prevention

Model business invariants explicitly.

```text
couponRedemptions < allowedLimit
```

and enforce them transactionally on the server.

---

# 39. Replay Attack

Attacker captures a valid request and sends it again.

```text
Valid request
     ↓
Captured
     ↓
Replay
```

### Prevention

* Nonces
* Timestamps
* Expiring tokens
* Idempotency keys
* Request signatures

---

# 40. Man-in-the-Middle — MITM

Attacker intercepts communication between parties.

```text
Client
  ↕
Attacker
  ↕
Server
```

### Prevention

* TLS
* Certificate validation
* Secure networking
* Avoid insecure protocols

---

# 41. TLS/Certificate Misconfiguration

Examples:

* Expired certificates
* Weak protocols
* Invalid certificate validation
* Plain HTTP
* Weak cipher configuration

### Prevention

Automated certificate management and modern TLS configurations.

---

# 42. DNS Attacks

Examples include:

* DNS spoofing
* DNS cache poisoning
* DNS hijacking
* DNS rebinding

### Prevention

* Secure DNS infrastructure
* DNSSEC where appropriate
* Network controls
* Proper hostname validation

---

# 43. API Rate-Limit Bypass

Application has a rate limit:

```text
100 requests/minute
```

but attacker finds alternate routes or identities to evade it.

### Prevention

Rate limiting should be designed around the actual abuse boundary:

```text
user
account
IP
API key
device
resource
tenant
```

rather than blindly relying on one identifier.

---

# 44. Denial of Service — DoS

Attacker exhausts:

```text
CPU
Memory
Threads
Connections
Database
Disk
Network
```

### Prevention

* Rate limiting
* Timeouts
* Resource quotas
* Backpressure
* Circuit breakers
* Load shedding
* Autoscaling

---

# 45. Distributed Denial of Service — DDoS

Many systems attack one target.

```text
Bot A ─┐
Bot B ─┤
Bot C ─┼──→ Application
Bot D ─┤
...    ┘
```

### Prevention

* CDN
* DDoS protection
* WAF
* Rate limiting
* Traffic filtering
* Capacity planning

---

# 46. ReDoS — Regular Expression DoS

A poorly designed regex can consume huge CPU for specially crafted input.

```text
Attacker input
      ↓
Complex regex
      ↓
CPU explosion
```

### Prevention

* Safe regex design
* Regex timeouts
* Input limits
* Avoid catastrophic backtracking

---

# 47. Memory Exhaustion

Example:

```text
POST /upload
```

Application allows unlimited payload size.

Attacker sends huge data repeatedly.

### Prevention

* Request size limits
* Streaming
* Memory quotas
* Timeouts
* Backpressure

---

# 48. Dependency / Supply-Chain Attack ⭐⭐⭐⭐⭐

Your application depends on:

```text
Library A
   ↓
Library B
   ↓
Library C
```

If a dependency is compromised, your application may become compromised.

### Prevention

* Dependency scanning
* SBOM
* Pin versions
* Verify package integrity
* Patch vulnerabilities
* Trusted repositories
* Software signing

---

# 49. Typosquatting

Attacker publishes a malicious package with a name similar to a popular package.

```text
legitimate-package
malicious-package
```

Developers accidentally install the wrong one.

### Prevention

* Verify package names
* Internal registries
* Dependency lock files
* Package allowlists

---

# 50. Dependency Confusion

An attacker publishes a public package with the same/similar name as an internal package.

Package managers may select the malicious public package.

### Prevention

* Private registries
* Package namespace controls
* Dependency pinning
* Registry configuration

---

# 51. Secret Leakage

Secrets accidentally enter:

```text
Git
Logs
Docker images
CI/CD
Config files
Chat messages
Tickets
```

Examples:

```text
API keys
passwords
private keys
tokens
cloud credentials
```

### Prevention

* Secret managers
* Secret scanning
* Short-lived credentials
* Rotation
* Never hardcode secrets

---

# 52. Cloud IAM Misconfiguration ⭐⭐⭐⭐⭐

Example:

```text
Storage bucket
     ↓
Public read
```

or:

```text
Service account
     ↓
Admin permissions
```

### Prevention

**Least privilege.**

Give identities only the permissions required.

---

# 53. Public Cloud Storage

Examples:

```text
Public S3 bucket
Public GCS bucket
Public Azure Blob
```

Potential result:

```text
Private data → Internet
```

### Prevention

* Private-by-default storage
* IAM
* Bucket policies
* Organization policies
* Continuous scanning

---

# 54. Cloud Metadata SSRF

This is an important **SSRF + cloud** combination.

```text
Internet
   ↓
Vulnerable application
   ↓
Cloud metadata service
   ↓
Temporary credentials
```

That's why cloud environments require both:

> **SSRF protection + strong IAM/network controls.**

---

# 55. Container Escape

Attacker compromises a container and attempts to reach the host.

```text
Container
    ↓
Host
    ↓
Other containers / infrastructure
```

### Prevention

* Don't run privileged containers
* Drop Linux capabilities
* Seccomp
* AppArmor/SELinux
* Read-only filesystems where possible
* Minimal images
* Patch kernel/container runtime

---

# 56. Kubernetes RBAC Misconfiguration ⭐⭐⭐⭐⭐

Example:

```text
Application pod
     ↓
ServiceAccount
     ↓
cluster-admin
```

Huge blast radius.

### Prevention

Use:

```text
Least-privilege RBAC
Namespaces
NetworkPolicies
Pod security controls
Minimal service accounts
```

---

# 57. Kubernetes Secret Exposure

Secrets may leak through:

```text
Logs
Environment variables
Manifests
Git
Container images
```

### Prevention

Use appropriate secret-management solutions and restrict RBAC/access.

---

# 58. Insecure Docker Image

Problems:

```text
Old OS
Vulnerable libraries
Root user
Secrets embedded
Unnecessary packages
```

### Prevention

* Minimal base images
* Image scanning
* Non-root containers
* SBOM
* Signed images
* Regular patching

---

# 59. Database Security Problems

Common examples:

```text
Public database
Weak password
Excessive permissions
No encryption
No auditing
Unencrypted backups
SQL injection
Sensitive data exposure
```

### Prevention

```text
Private networking
IAM
Encryption
Least privilege
Parameterized queries
Auditing
Backup protection
```

---

# 60. Encryption Failures

Examples:

```text
HTTP instead of HTTPS
Weak algorithms
Hardcoded encryption keys
Poor key management
Unencrypted sensitive data
```

### Prevention

Use well-reviewed cryptographic libraries and centralized key management.

**Don't invent your own cryptography.**

---

# 61. JWT Vulnerabilities

Common mistakes:

```text
No signature validation
Accepting unexpected algorithms
Long-lived tokens
Missing issuer validation
Missing audience validation
Sensitive information in payload
Poor key management
```

### Prevention

Validate:

```text
signature
issuer
audience
expiration
algorithm
```

and keep signing keys secure.

---

# 62. OAuth Misconfiguration

Potential problems:

* Incorrect redirect URI validation
* Missing state protection
* Improper token handling
* Overly broad scopes
* Token leakage

### Prevention

Use well-established OAuth/OIDC libraries and carefully validate:

```text
redirect_uri
state
nonce
scope
issuer
audience
```

---

# 63. Token Leakage

Tokens may leak through:

```text
URLs
logs
browser history
referrers
source code
screenshots
Git
```

### Prevention

* Short-lived tokens
* Secure storage
* Don't put secrets in URLs
* Redact logs
* Rotation/revocation mechanisms

---

# 64. Account Enumeration

Application reveals whether an account exists.

For example:

```text
"user doesn't exist"
```

versus:

```text
"incorrect password"
```

### Prevention

Use appropriately generic responses and monitor abuse.

---

# 65. User Enumeration Through Timing

Even if responses look identical:

```text
Existing user → 100ms
Non-existing user → 10ms
```

timing can reveal information.

### Prevention

Where appropriate, normalize sensitive authentication behavior and rate-limit enumeration attempts.

---

# 66. Mass Assignment

Client submits fields it shouldn't control.

```json
{
  "name": "Ganesh",
  "role": "ADMIN"
}
```

### Prevention

Explicit DTOs / allowlists of writable fields.

---

# 67. Excessive Data Exposure

API returns:

```json
{
  "name": "...",
  "email": "...",
  "salary": "...",
  "internalId": "...",
  "securityData": "..."
}
```

when the frontend only needs:

```json
{
  "name": "..."
}
```

### Prevention

Return only required fields.

---

# 68. API Parameter Pollution

Duplicate parameters:

```text
?id=10&id=20
```

Different layers may interpret them differently.

### Prevention

Define exactly how duplicate parameters are handled and reject ambiguous requests.

---

# 69. WebSocket Security

WebSockets can have:

* Missing authentication
* Missing authorization
* Cross-origin issues
* Message injection
* Resource exhaustion

### Prevention

Authenticate and authorize connections and individual operations, validate messages, and apply limits.

---

# 70. GraphQL Security

Potential problems:

* Excessive query depth
* Excessive query complexity
* Authorization failures
* Introspection exposure
* Data over-fetching

### Prevention

* Query depth limits
* Complexity limits
* Authentication
* Field-level authorization
* Rate limits
* Proper production configuration

---

# 71. Logging Vulnerabilities

Logging user input without care can lead to:

* Log injection
* Credential leakage
* PII exposure
* Log flooding

### Prevention

Structured logging + sanitization + redaction.

---

# 72. Log Injection

Attacker-controlled input manipulates log records.

### Prevention

Use structured logging rather than concatenating raw strings.

---

# 73. Sensitive Information in Logs

Never casually log:

```text
password
access token
refresh token
credit-card data
private key
session ID
```

Use:

```text
requestId
traceId
correlationId
```

instead.

---

# 74. Debug Mode Exposure

Production:

```text
DEBUG=true
```

can reveal:

```text
Stack traces
Environment variables
Database information
Internal paths
Configuration
```

### Prevention

Disable debug functionality in production.

---

# 75. Insecure Direct Database Access

Application gives users direct access to database-like functionality.

### Prevention

Use a controlled service/API boundary with authorization.

---

# 76. Insider / Privileged User Abuse

Not every threat is external.

A legitimate employee/service account can misuse privileges.

### Prevention

* Least privilege
* Separation of duties
* Auditing
* Just-in-time access
* Access reviews
* Monitoring

---

# 77. Data Tampering

Attacker modifies data without authorization.

Examples:

```text
Order amount
Inventory
Customer address
Payment status
Product price
```

### Prevention

* Authorization
* Integrity controls
* Transactions
* Audit logs
* Digital signatures where appropriate

---

# 78. Repudiation

User performs an action but later claims:

> "I didn't do that."

### Prevention

Maintain reliable audit trails:

```text
Who
What
When
Where
Request ID
Result
```

without logging sensitive secrets.

---

# 79. Supply-Chain CI/CD Attack ⭐⭐⭐⭐⭐

Attacker compromises:

```text
Git repository
      ↓
CI/CD
      ↓
Build
      ↓
Production
```

### Prevention

* Protected branches
* MFA
* Signed commits/artifacts where appropriate
* Build isolation
* Least-privileged CI credentials
* Artifact verification
* Secrets protection

---

# 80. Artifact / Build Tampering

Build artifact is modified between build and deployment.

### Prevention

```text
Build
 ↓
Sign artifact
 ↓
Verify artifact
 ↓
Deploy
```

---

# 81. Dependency Vulnerability

Your application uses a library containing a known vulnerability.

### Prevention

Automated:

```text
SCA
Dependency scanning
SBOM
Patch management
```

---

# 🧠 The Most Important Recognition Table

When you see this:

| Situation                                       | Think                         |
| ----------------------------------------------- | ----------------------------- |
| User input → HTML                               | **XSS**                       |
| User input → SQL                                | **SQL Injection**             |
| User input → OS command                         | **Command Injection**         |
| User controls URL → server requests it          | **SSRF**                      |
| Victim browser → unwanted authenticated request | **CSRF**                      |
| Change `/user/123` → `/user/124`                | **IDOR/BOLA**                 |
| User accesses admin API                         | **Broken Authorization**      |
| `../` in filename                               | **Path Traversal**            |
| User controls file include                      | **LFI/RFI**                   |
| XML parser → external entities                  | **XXE**                       |
| Untrusted serialized object                     | **Deserialization**           |
| Malicious file upload                           | **Unrestricted File Upload**  |
| Error reveals internals                         | **Information Disclosure**    |
| Debug enabled in production                     | **Security Misconfiguration** |
| Weak/default configuration                      | **Security Misconfiguration** |
| Too many permissions                            | **Privilege / IAM issue**     |
| Service account has admin                       | **Excessive Privilege**       |
| Container reaches host                          | **Container Escape**          |
| Package compromised                             | **Supply Chain Attack**       |
| Duplicate request causes double action          | **Replay/Race Condition**     |
| Two requests manipulate same state              | **Race Condition**            |
| Application abuses legitimate workflow          | **Business Logic Flaw**       |
| Unlimited expensive requests                    | **DoS/Resource Exhaustion**   |
| Regex consumes huge CPU                         | **ReDoS**                     |
| Redirect controlled by user                     | **Open Redirect**             |
| Website embedded to trick clicks                | **Clickjacking**              |
| Proxy/server parse HTTP differently             | **Request Smuggling**         |
| API returns too much information                | **Excessive Data Exposure**   |
| Client sets `role=admin`                        | **Mass Assignment**           |
| Public bucket                                   | **Cloud Misconfiguration**    |
| Token in logs/URL                               | **Credential/Token Leakage**  |
| Weak JWT validation                             | **JWT Security Issue**        |
| OAuth redirect manipulation                     | **OAuth Misconfiguration**    |

---

# 🎯 The 15 You Should Know FIRST

If you're preparing for your Walmart Secure Software modules, memorize these first:

```text
1. XSS
2. SQL Injection
3. SSRF
4. CSRF
5. IDOR / BOLA
6. Broken Authentication
7. Broken Authorization
8. Security Misconfiguration
9. Path Traversal
10. Command Injection
11. XXE
12. Insecure Deserialization
13. File Upload
14. Race Conditions
15. Sensitive Data Exposure
```

Then move to:

```text
16. OAuth/OIDC
17. JWT
18. CORS
19. CSP
20. Clickjacking
21. Request Smuggling
22. Cache Poisoning
23. Supply Chain
24. Cloud IAM
25. Container/Kubernetes security
26. API security
27. Business Logic
28. DoS
29. Cryptographic failures
30. Secrets management
```

---

# 🏆 Staff Engineer Security Checklist

When you're designing one of your **Spring Boot / Kafka / Flink / Airflow / GCP / Kubernetes** systems, don't just ask:

> "Does it work?"

Ask these:

```text
                    SECURITY REVIEW
                          │
        ┌─────────────────┼──────────────────┐
        ▼                 ▼                  ▼
   Authentication    Authorization       Input
        │                 │                  │
       Who?           Allowed?          Trusted?
        │                 │                  │
        └─────────────────┼──────────────────┘
                          ▼
                       Data
                          │
              ┌───────────┼────────────┐
              ▼           ▼            ▼
             DB         Kafka         APIs
              │           │            │
              └───────────┼────────────┘
                          ▼
                     Infrastructure
                          │
              ┌───────────┼────────────┐
              ▼           ▼            ▼
             IAM        Network      Secrets
              │           │            │
              └───────────┼────────────┘
                          ▼
                    Observability
                          │
                  Audit + Detection
```

And ask:

### **1. Authentication**

Who is calling me?

### **2. Authorization**

Are they allowed to do this?

### **3. Input**

Can they control anything?

### **4. Injection**

Where does their input go?

### **5. Data**

Can they access another user's data?

### **6. Secrets**

Could credentials leak?

### **7. Network**

Can this service reach something it shouldn't?

### **8. IAM**

Does this service have more privileges than necessary?

### **9. Availability**

Can one user consume all my resources?

### **10. Integrity**

Can requests be replayed, reordered, duplicated, or tampered with?

### **11. Observability**

Would I know an attack happened?

### **12. Blast radius**

If this service is compromised, **what else can the attacker reach?**

That last question is particularly important at **Staff/Architect level**: security isn't just preventing individual vulnerabilities; it's designing the system so that **one compromised component doesn't become a company-wide compromise**.
