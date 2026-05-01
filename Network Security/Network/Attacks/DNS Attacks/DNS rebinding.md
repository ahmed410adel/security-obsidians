## Definition
A DNS rebinding attack is a client-side attack that tricks a victim’s browser into accessing internal or local network services by changing the IP address that a malicious domain resolves to.

It abuses:
- Low DNS TTL
- Browser Same-Origin Policy (SOP)
- Lack of IP-to-origin binding


## Core Idea

Browsers enforce security based on:
protocol + domain + port
They do **not** bind the origin to a fixed IP address.
If a domain resolves to a different IP later, the browser still treats it as the same origin.
This allows an attacker-controlled domain to later point to:
- 127.0.0.1
- 192.168.x.x
- 10.x.x.x
- 169.254.169.254 (cloud metadata)
## Attack Flow
1. Victim visits `evil.com`.
2. DNS resolves to attacker’s public server.
3. Attacker serves malicious JavaScript.
4. DNS TTL expires (very low TTL).
5. DNS now resolves `evil.com` to a private/internal IP.
6. JavaScript sends requests to `evil.com`.
7. Browser connects to internal IP but keeps origin as `evil.com`.
8. Internal service response becomes accessible to attacker script.

## Why It Works
- DNS responses can change at any time.
- DNS does not restrict mapping to private IPs.
- Browser trusts domain name, not IP.
- No strict binding between initial IP and future resolutions.
This creates a trust gap between DNS and Same-Origin Policy.

---

## Typical Targets
- Home router admin panels
- Internal company dashboards
- Localhost development servers
- Docker APIs
- Cloud metadata services (AWS, GCP, Azure)

## Limitations
- Requires victim interaction.
- Does not automatically bypass authentication.
- Modern browsers implement partial protections.
- HTTPS reduces feasibility (certificate mismatch).
- Proper Host header validation blocks it.
- 
## Defensive Measures

Server-side:
- Validate Host header.
- Require authentication (even internally).
- Implement CSRF protection.
- Restrict services to localhost only.

Network-level:
- Firewall internal services.
- Avoid exposing admin APIs.

Browser-level:
- Modern browsers block some private-IP rebinding.


## Comparison

|Attack|Nature|
|---|---|
|CSRF|Abuses session trust|
|SSRF|Server-side request abuse|
|DNS Poisoning|DNS cache corruption|
|DNS Rebinding|Browser origin trust abuse|

DNS rebinding is essentially **client-side SSRF via the victim’s browser**.