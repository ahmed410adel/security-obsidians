# Input validation & injection
1. [ ] SQL Injection (SQLi)
2. [ ] Blind SQL Injection (Boolean / Time-based)
3. [ ] Error-based SQL Injection
4. [ ] UNION-based SQL Injection
5. [ ] NoSQL Injection (e.g., MongoDB)
6. [ ] XPath Injection
7. [ ] LDAP Injection
8. [ ] Command Injection (OS Command Injection)
9. [ ] Shell injection via web parameters
10. [ ] Template Injection (SSTI)
11. [ ] Server-Side Template Injection (SSTI) — remote code variants
12. [ ] XML External Entity (XXE)
13. [ ] Expression Language (EL) Injection
14. [ ] CRLF Injection (HTTP Response Splitting)
15. [ ] Header Injection
16. [ ] SSI Injection (Server Side Includes injection)
17. [ ] Code injection via eval-like functions
18. [ ] Format string injection
# Cross site scripting & client-side
1. [ ] Reflected Cross-Site Scripting (XSS)
2. [ ] Stored/Persistent XSS
3. [ ] DOM-based XSS
4. [ ] Self-XSS (social-engineering assisted)
5. [ ] Mutation XSS / polyglot XSS
6. [ ] Client-side template XSS (mustache, handlebars)
7. [ ] Event-handler injection (onmouseover etc.)
8. [ ] CSP bypass for XSS (variants)
# Cross-site request forgery & related
1. [ ] Cross-Site Request Forgery (CSRF)
2. [ ] Login CSRF
3. [ ] OAuth CSRF / redirect_uri misuse
4. [ ] CSRF via CORS misconfiguration
# Authentication / session attacks
1. [ ] Broken authentication
2. [ ] Session fixation
3. [ ] Session hijacking (cookie theft — network vectors)
4. [ ] Predictable session tokens
5. [ ] Brute-force credential stuffing
6. [ ] Password spraying
7. [ ] Plaintext password disclosure
8. [ ] Weak password policy exploitation
9. [ ] Multi-factor bypass (MFA fatigue, OTP interception)
10. [ ] Insecure "remember me" implementations
11. [ ] Insecure password reset flows (token predictability)
12. [ ] Credential stuffing via API endpoints
13. [ ] Session replay attacks
14. [ ] Token leakage in referrers/logs
# Authorization & access control
1. [ ] Insecure direct object reference (IDOR)
2. [ ] Vertical privilege escalation
3. [ ] Horizontal privilege escalation
4. [ ] Forced browsing / directory brute force
5. [ ] Broken access control (misconfigured ACLs)
6. [ ] Access control bypass via parameter tampering
7. [ ] Business logic abuse / logic flaws
8. [ ] Race condition in access control (TOCTOU)
# File & resource handling
1. [ ] Unrestricted File Upload
2. [x] Path traversal (directory traversal)
3. [x] Local File Inclusion (LFI)
4. [x] Remote File Inclusion (RFI)
5. [ ] Arbitrary file write
6. [ ] Insecure file permissions disclosure
7. [ ] Sensitive file exposure (backup/.git/.env)
8. [ ] Content-type spoofing / upload bypass
9. [ ] Archive/Zip slip (ZIP path traversal)
10. [ ] Picture metadata leakage (EXIF)
# Server-side vulnerabilities & misconfigurations
1. [ ] Remote Code Execution (RCE) via webapp
2. [ ] Deserialization vulnerabilities (insecure deserialization)
3. [ ] PHP object injection
4. [ ] Unsafe deserialization (Java, Python, PHP)
5. [ ] Command chaining via web inputs
6. [ ] HTTP request smuggling
7. [ ] HTTP request splitting
8. [ ] Unsafe use of eval/exec on server-side
9. [ ] Directory listing exposure
10. [ ] Misconfigured default credentials on web apps
11. [ ] Information disclosure via verbose errors
# API & microservices
1. [ ] API endpoint enumeration
2. [ ] Broken object level authorization (BOLA) — API IDOR
3. [ ] Mass assignment (overposting)
4. [ ] Insecure direct object reference in APIs
5. [ ] Improper rate limiting (API abuse)
6. [ ] Excessive data exposure (over-sharing in JSON)
7. [ ] API token leakage / insecure storage
8. [x] Insecure CORS configuration (wildcard origins, credentials)
9. [x] API parameter pollution
10. [ ] GraphQL-specific issues (introspection abuse, injection)
11. [ ] REST to GraphQL mapping issues
12. [ ] SOAP WS-* related vulnerabilities (XML injection)
# Client-side security & supply-chain
1. [ ] Dependency (JS/CSS) supply-chain compromise
2. [ ] Malicious third-party script inclusion (third-party compromise)
3. [ ] Cross-origin resource inclusion abuse
4. [ ] Subresource Integrity (SRI) bypass/omission issues
5. [ ] Typosquatting / CDN compromise (malicious CDN packages)
6. [ ] DOM clobbering (overriding built-ins via element names)
7. [ ] Prototype pollution (client & server JS)
8. [ ] Package manager poisoning (npm, pip malicious packages)
9. [ ] Inline script CSP bypass via unsafe-eval/unsafe-inline
# Cryptography & transport
1. [ ] TLS downgrade attacks / SSL stripping (web-specific)
2. [ ] Weak TLS cipher usage exposure
3. [ ] Broken certificate validation (trust on first use flaws)
4. [ ] Mixed content (HTTPS page loading HTTP subresources)
5. [ ] Sensitive data over HTTP (cleartext secrets)
6. [ ] Insecure cryptographic storage (weak hashing, no salt)
7. [ ] Replay of encrypted tokens (no freshness controls)
# Business logic & workflow abuse
1. [ ] Price manipulation / tampering (e-commerce)
2. [ ] Coupon / discount abuse (logic bypass)
3. [ ] Inventory race conditions (double spending)
4. [x] Order tampering / fulfillment abuse
5. [ ] Workflow bypass (skip approval steps)
6. [ ] Account takeover via logic flaws (email change loops)
7. [ ] Abuse of free-trial / credit systems
# Client-server protocol & parsing
1. [ ] HTTP verb tampering (using PUT/DELETE when unsupported)
2. [x] HTTP parameter pollution (HPP)
3. [ ] Content-type confusion leading to XSS/exec
4. [ ] Multipart/form-data parser quirks exploitation
5. [ ] CRLF injection into logs / downstream systems
# Caching, CDN & edge
1. [ ] Cache poisoning (web cache poisoning)
2. [ ] HTTP cache control misconfiguration leaks private content
3. [ ] CDN cache TTL abuse (stale sensitive content)
4. [ ] Edge function (Cloudflare Workers/AWS Lambda@Edge) misconfig
5. [ ] Host header injection (virtual host confusion)
6. [ ] Host header poisoning (password reset attack vectors)
7. [ ] Reverse proxy bypass via path normalization quirks
# CSRF-adjacent & cross-origin
1. [x] Cross-Origin Resource Sharing (CORS) misconfig abuse
2. [ ] Cross-origin leaks (postMessage mishandling)
3. [ ] Timing attacks across origins (cross-origin timing)
4. [ ] Spectre-like browser side-channel (web attack surface)
# Logging & monitoring abuse
1. [ ] Log injection / log forging
2. [ ] Audit trail tampering via web inputs
3. [ ] SIEM evasion through structured payloads
# Phishing & UI attacks
1. [x] Clickjacking (UI redress attack)
2. [ ] UI redress / framebusting bypass
3. [ ] Open redirect (unvalidated redirect)
4. [ ] URL shortener abuse / redirector chaining
5. [ ] Homograph attacks (IDN phishing)
6. [ ] Credential phishing via spoofed web pages (UI-based)
# Search, indexing & SEO abuse
1. [ ] Search engine poisoning (malicious content injection)
2. [ ] Sitemap manipulation for exposure
3. [ ] Robots.txt disclosure abuse (listing sensitive paths)
# WebSocket's & Realtime
1. [ ] WebSocket injection / unsecured WS endpoints
2. [ ] WebSocket authentication bypass
3. [ ] Cross-protocol attacks via WebSocket endpoints
# Single-page apps & modern frameworks
1. [ ] Client-side SSRF (browser-based SSRF through fetch)
2. [ ] State management leakage (exposed tokens in SPA state)
3. [ ] Insecure use of Service Workers (cache poisoning, request interception)
4. [ ] Insecure IndexedDB/localStorage usage (sensitive data stored)
5. [ ] Manifest / PWA misconfig leading to phishing
# Storage & persistence
1. [ ] Sensitive data exposure in backups / snapshots (accessible via web)
2. [ ] Insecure use of object storage (S3 bucket misconfig)
3. [ ] Publicly accessible cloud storage endpoints (exposed blobs)
# Server & infrastructure chaining
1. [ ] Server-Side Request Forgery (SSRF)
2. [ ] Blind SSRF (no direct response observations)
3. [ ] SSRF to internal service discovery (metadata endpoints)
4. [ ] SSRF leading to cloud metadata access (IMDS)
5. [ ] SSRF to port scanning / internal network mapping
# Monitoring, analytics & third-party abuse
1. [ ] Analytics cookie poisoning
2. [ ] Pixel/Beacon abuse for data exfiltration
3. [x] Third-party widget CORS/credential leaks
# Authentication protocol & federation
1. [ ] OAuth misconfiguration (open redirect, token leakage)
2. [ ] OpenID Connect (OIDC) flow confusion / ID token issues
3. [ ] SAML vulnerabilities (XML signatures, assertion spoofing)
4. [ ] JWT issues (none/alg=none, token disclosure, weak signing)
5. [ ] Token replay / insufficient revocation
# Advanced & niche
1. [ ] Click-fraud / ad-fraud (web ad abuse)
2. [ ] Cache key collision attacks
3. [ ] Resource exhaustion via complex regex (ReDos / Regular expression DoS)
4. [ ] Prototype cache poisoning in JS engines
5. [ ] Application logic fingerprinting for advanced automated attacks
6. [ ] Edge case HTTP parser evasion (different server implementations)
# Mobile / app-web interface
1. [ ] Insecure mobile web API endpoints (exposed debug endpoints)
2. [ ] Insecure deep link / universal link handling (open redirect / intent hijack)
3. [ ] Mobile webview injection (fragment + JS bridges exploitation)
# Cloud-native & container ecosystems
1. [ ] Misconfigured OAuth / API gateways (expose admin APIs)
2. [ ] Kubernetes dashboard exposure / API abuse
3. [ ] Container image supply-chain compromise (web-facing images)
4. [ ] Insecure service mesh config exposing internal services
# Privacy & data exfiltration
1. [ ] Mass data scraping via public API abuse
2. [ ] API parameter fuzzing for data exposure
3. [ ] Covert exfiltration via image/GIF/metadata or steganography in web responses
# Red-team / persistence & pivoting via web
1. [ ] Web-based C2 channels (beaconing via web traffic)
2. [ ] Living-off-the-land abuse (legit web features used for malicious persistence)
3. [ ] Webshell upload and persistence (multi-stage)
4. [ ] Admin interface compromise and pivoting
# Emerging / protocol-specific
1. [ ] QUIC-specific token & header misuse
2. [ ] HTTP/3 flow control / resource exhaustion (emerging)
3. [ ] GraphQL deep query exhaustion (complex queries causing DoS)