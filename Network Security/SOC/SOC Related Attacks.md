# 1. Single-Flux
**Core idea:** only the domain’s A/AAAA records rotate quickly. The domain’s NS records stay normal/constant.

**How it works**
1. Attacker controls a botnet (many compromised hosts).
2. The domain’s A records are updated often (every seconds/minutes) to point at different botnet IPs.
3. Each botnet node proxies traffic to the real malicious server (or serves content directly).
4. TTL is kept very low so resolvers refresh the A record frequently.

**Why attackers use it**
- Hides the real server behind many proxies.
- Makes IP-based blocking ineffective (when you block one IP, others appear quickly).

**Detection signals**
- Very frequent changes in A records.
- IPs are from residential/consumer ASNs or geographically scattered home IPs.
- Low TTL values.

**Example (simplified)**
- Lookup 1: `bad.example A -> 10.1.1.5, 10.1.1.9 (TTL=60)`
- Two minutes later: `bad.example A -> 192.0.2.3, 198.51.100.7 (TTL=60)`

# 2. Double-Flux
**Core idea:** both the domain’s A records **and** its NS records rotate. The authoritative DNS servers themselves are transient and served by botnet nodes.

**How it works**
1. Botnet nodes act as authoritative DNS servers (or as proxies for authoritative answers).
2. The domain’s NS records point to hostnames that resolve to botnet IPs, and those NS entries are changed frequently.
3. The A records for the domain also change as in single-flux.
4. Because the DNS servers are also moving, the entire DNS answering infrastructure is distributed and ephemeral.

**Why it’s more powerful**
- Harder to block or takedown because you can’t just target the DNS servers — those are temporary and distributed across many compromised hosts too.
- Adds an extra layer of indirection and resilience.

**Detection signals**
- Frequent changes in NS records (not just A records).
- NS hostnames resolving to IPs in residential networks or many unrelated ASNs.
- Inconsistent DNS behavior across different resolvers.

**Example (simplified)**
- Lookup 1:
    - `bad.example NS -> ns1.bad.example, ns2.bad.example`
    - `ns1.bad.example A -> 10.1.1.5`
    - `bad.example A -> 10.1.1.5, 10.1.1.9`
- Later: `bad.example NS -> ns7.bad.example, ns9.bad.example` (pointing to different botnet hosts)

# 3. Punycode
a way of converting words that cannot be written in ASCII, into a Unicode ASCII encoding.
`adıdas.de`  which has the Punycode of  `http://xn--addas-o4a.de/`