## Definition

DNS tunneling is a **data exfiltration and command-and-control technique** that abuses DNS queries to send data out of a restricted network.

It works because:
- DNS (UDP/53) is almost always allowed outbound.
- DNS traffic is rarely deeply inspected.
- Queries can contain arbitrary subdomain data.

## Core Idea
Instead of sending data via HTTP or TCP, the attacker:

1. Encodes the data (Base32 / Base64 / Hex).
2. Splits it into chunks (DNS label limit = 63 chars).
3. Inserts chunks into subdomains.
4. Sends DNS queries to an attacker-controlled domain.

Example:
`U3VwZXJTZWNyZXQxMjM.evil.com`

The attacker controls `evil.com` and logs all incoming DNS queries, then decodes the data.

## DNS Protocol Constraints

- Max label length: **63 bytes**
- Max full domain length: **253 bytes**
- Default UDP DNS packet size: ~512 bytes
- Unsafe characters must be encoded

Because of these limits:
- Data must be chunked
- Sequence numbers are often used
- High number of DNS requests = noisy


## Two Types

### 1) Simple DNS Exfiltration
- One-way communication
- Data embedded in query
- No response channel needed

### 2) Full DNS Tunnel (C2)
- Bidirectional communication
- Commands sent via DNS responses (often TXT records)
- Maintains session IDs
- Often encrypted

Tools:
- `iodine`
- `dnscat2`
- `DNSCat`

## Why It Works
DNS resolution chain:
Victim → Resolver → Root → TLD → Authoritative DNS (attacker)
If attacker controls the authoritative server, they receive every query containing exfiltrated data.
## Detection Indicators

1. High entropy subdomains  
    (random-looking long strings)
2. Very long labels (50+ characters)
3. Excessive TXT queries
4. High frequency DNS requests (beaconing)
5. Newly registered suspicious domains

## Defensive Controls
- Force DNS through internal resolvers
- Block direct external DNS
- DNS inspection / entropy detection
- RPZ (Response Policy Zones)
- Monitor unusual TXT traffic

## Important Clarifications
- DNS tunneling is usually **post-exploitation**
- It requires code execution or SSRF to trigger DNS queries
- It is rarely initial access in bug bounty

## Mental Model

> DNS tunneling hides data inside domain names because DNS is trusted and usually allowed outbound.

It turns DNS into a covert communication channel.