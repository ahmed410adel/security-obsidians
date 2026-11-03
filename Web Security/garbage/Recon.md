# Techniques
#### 1. Target Scoping
idea behind target scoping is :
1. list *in* and *out* scope domains or subdomains
2. Identify primary assets (main websites, apps, APIs).
3. Document everything so you don’t miss targets.
##### make manual research
- walk on the website
- LinkedIn
- Crunchbase => to get the company acquisitions
#### 2. Collect Domains
- `whois`
	use it manually by collecting (Registrant Organization, Registrant Email, Nameservers) and make `revers whois` on them
- `reverse whois` + `viewDNS`
- `ct logs`
	- `crt.sh` 
		could be used on CLI `curl -s "https://crt.sh/?q=%25.example.com&output=json" | jq '.[].name_value' | sort -u`
	- `amass`
		could be used on CLI `amass enum -passive -d example.com`
- `amass`
	get ASN number
- `bgp.he.netg`
	get IP ranges
- `google dorks`
	example for dorks `site:example.*`
#### 3. Subdomain Enumeration
##### Tools :
1. `subfinder`
2. `assetfinder`
3. `amass`
4. `crt.sh`
	- `curl -s 'https://crt.sh/?q=example.com&output=json' | jq -r '.[].name_value' >> subdomains.txt`
5. `waybackurls`
6. `Sublist3r`
7. fuzzing
#### 4. Prove That This Hosts Are Alive
use `httpx` to prove that
- `httpx -status-code -title -tech-detect -o live_subs.txt`
	`-tech-detect` → Spots WordPress, Jenkins, or custom frameworks.
#### 3. Visual Proof
#### 4. Directory Fuzzing & Try To Access Robots.txt File
- use `fuff` search for 200 and 403 responses
##### For 403 responses try to access them using the following techniques
- `X-Originating-IP: 127.0.0.1`
- `X-Forwarded-For: 127.0.0.1`
- `X-Forwarded: 127.0.0.1`
- `Forwarded-For: 127.0.0.1`
- `X-Remote-IP: 127.0.0.1`
- `X-Remote-Addr: 127.0.0.1`
- `X-ProxyUser-Ip: 127.0.0.1`
- `X-Original-URL: 127.0.0.1`
- `Client-IP: 127.0.0.1`
- `True-Client-IP: 127.0.0.1`
- `Cluster-Client-IP: 127.0.0.1`
- `X-ProxyUser-Ip: 127.0.0.1`
- `Host: localhost`
- `X-Forwarded-For: https://127.0.0.1/admin`
or try 
- `example/./admin`
- `example/file/../admin`
- `encode last char at the link`
- `double-url-encode last char at the link`
#### 5. Look For Hidden Parameters
using `arjun`
#### 6. Look AT JS Files
- `JSFinder`
#### 7. Scanning


---
### Phase 1 — Passive Recon (no contact with target)
**Domain & registration**
- WHOIS lookup (registrar, creation/expiry date, registrant if not privacy-protected)
- Historical WHOIS (whoxy, SecurityTrails) to catch past owner info
- Reverse WHOIS to find related domains registered by same org
***OK***


**DNS footprint**
- A, AAAA, MX, NS, TXT, SOA, CNAME records
- SPF/DKIM/DMARC records (TXT) — reveals mail infra, sometimes internal hostnames
- Zone transfer attempt (AXFR) — almost always blocked but worth checking
- DNS history (SecurityTrails, DNSDumpster) — old IPs can reveal origin behind a CDN


**Subdomain enumeration**
- Certificate Transparency logs (crt.sh, censys.io) — single best source
- Passive aggregators: SecurityTrails, VirusTotal, Shodan subdomain data
- Brute-force wordlists (amass, subfinder, gobuster with resolver) as active follow-up


**IP & hosting**
- Resolve all subdomains to IPs, cluster by IP/ASN
- ASN/BGP lookup (bgp.he.net) to find full IP ranges owned by the org
- Shodan/Censys scan of discovered IPs for exposed services, banners, SSL cert data
- CDN/WAF detection (Cloudflare, Akamai, etc.) and attempts to find origin IP behind it


**Search engine & cache intel**
- Google/Bing dorking: `site:`, `inurl:`, `filetype:`, `intitle:` for exposed files, admin panels, error pages, login portals
- Cached/archived versions: Wayback Machine (archive.org), Google cache — reveals old pages, removed content, past tech stack
- Search for leaked credentials/paste sites tied to the domain (HaveIBeenPwned domain search, dehashed if authorized)

**People & org intel**
- LinkedIn/employee enumeration → builds username patterns for later
- GitHub/GitLab search for the org or employees — leaked API keys, internal paths, config files
- Job postings — often reveal exact tech stack (“looking for a Laravel + AWS + Jenkins admin”)
- Social media, metadata in public documents (PDFs/DOCs on the site — exiftool for author names, software versions, internal paths)

### Phase 2 — Active Recon (light touch, direct contact)
**Web server & tech stack fingerprinting**
- HTTP headers (Server, X-Powered-By, cookies) via curl/httpx
- Wappalyzer / whatweb for full tech stack (CMS, JS frameworks, analytics, CDN)
- Favicon hash matching (Shodan favicon search) to identify platform even when headers are stripped
- TLS/SSL cert inspection (issuer, SANs — SANs often leak other subdomains)
- robots.txt and sitemap.xml — map disallowed paths (often the interesting ones) and full URL inventory

**Content & structure mapping**

- Full crawl (Burp Spider, OWASP ZAP, httpx+katana) to enumerate every link, form, parameter, and JS file
- JS file analysis — extract endpoints, API routes, hardcoded keys/secrets (tools: LinkFinder, SecretFinder)
- Directory/file brute-forcing (ffuf, gobuster) against common wordlists + CMS-specific lists
- Parameter discovery (Arjun, paramspider) for hidden GET/POST params
- API endpoint discovery — check for `/api/`, `/graphql`, Swagger/OpenAPI docs, `.well-known/`

**CMS-specific (relevant to your WordPress site)**

- WPScan full enumeration: users, themes, plugins + versions, upload directories, readme files, config backups (`wp-config.php.bak`), XML-RPC exposure, REST API user enumeration (`/wp-json/wp/v2/users`)
- Theme/plugin changelog diffing against known CVEs
- Check `/wp-content/uploads/` for directory listing or unindexed files

**Input surface mapping** (every place the app accepts data)

- Every form field, file upload, URL parameter, cookie, HTTP header the app reads, hidden fields
- For your site specifically: the upload page (file type/size validation, stored-file handling, metadata stripping) and the contact page (field validation, mail injection potential) deserve full parameter-level mapping even though they’re “just two pages” — stored XSS findings like the one you had usually hide in exactly this kind of narrow input surface

**Email & auth surface**

- Enumerate login portals, password reset flows, registration forms
- Check for exposed admin panels (`/wp-admin`, `/administrator`, etc.)
- 2FA/SSO presence

### Phase 3 — Organize & Cross-Reference

Build this into a structured asset inventory so nothing falls through gaps:

|Category|What to capture|
|---|---|
|Domains/Subdomains|full list + status (live/dead) + IP|
|IPs/ASNs|ranges, hosting provider, geolocation|
|Tech stack|CMS, plugins+versions, server, JS libs, CDN/WAF|
|Open ports/services|from Shodan/Censys + your own nmap scan|
|Input surfaces|every form/upload/param, by URL|
|Exposed files|backups, configs, logs, `.git`, `.env`|
|People/accounts|usernames, emails, leaked creds|
|Historical data|old IPs, archived pages, past tech|

A few tool chains that cover this almost completely: `subfinder → httpx → katana/gau → nuclei` for automated surface mapping, plus manual WHOIS/crt.sh/Shodan/Wayback passes for the parts automation misses.

Want me to turn this into a checklist/tracker artifact you can actually fill in as you go against your WordPress site?