## 1. Open Recursive Resolver Abuse
##### What is an Open Recursive Resolver
A resolver should normally answer **only its own users**.

Example:
- ISP DNS → answers only ISP customers
- Corporate DNS → answers only internal network

But sometimes servers are misconfigured. An **open recursive resolver** is a DNS server that accepts recursive queries from **anyone on the internet**. This means the server will perform DNS lookups for **any external user**.

###### There are three main abuses.
#### 1. DNS Amplification
A DNS amplification attack is a type of **DDoS (Distributed Denial of Service)** attack where:
*An attacker sends small DNS requests that trigger much larger responses, and those responses are sent to a victim instead of the attacker.*
##### The Core Weakness (Why This Works)
DNS over UDP does **not verify source IP addresses**.
UDP is connectionless:
- No handshake
- No verification
- Server just replies to the source IP in the packet

So the attacker can:
Fake (spoof) the source IP
And put the victim’s IP there.
The DNS server will then send the response to the victim.
##### Where Does the “Amplification” Come From?
The trick is requesting records that generate **large responses**.
Examples:
- `ANY` queries (older technique)
- DNSSEC-enabled domains
- Large TXT records
- DNSKEY records
#### 2. Hiding attacker identity (DNS proxying)
#### 3. Help DNS Cache Poisoning
#### 4. DNS Reflection
The DNS server becomes a **reflector**.
Why attackers like reflection ?
hides attacker IP and victim receives traffic from legitimate servers

|Feature|Reflection|Amplification|
|---|---|---|
|Purpose|hide attacker|increase traffic|
|Technique|spoof IP|large response|
|Requirement|server reply|response larger than request|

## 2. DNS Forwarder Abuse
A DNS forwarder is a DNS server that **does not resolve queries itself**. Instead, it **forwards DNS requests to another DNS resolver** (for example ISP DNS or internal DNS).

Example flow:
`Client → DNS Forwarder → Upstream DNS → Internet`

Organizations use forwarders for:
- centralized DNS management
- caching responses
- logging DNS traffic
- filtering domains

DNS Forwarder Abuse occurs when a **misconfigured DNS forwarder allows external users to send recursive DNS queries through it**.

This usually happens when:
- recursion is enabled
- the server is publicly accessible
- there is no source IP restriction
The forwarder becomes a **DNS proxy** that attackers can abuse.

#### Common Attack Scenarios
**1. Internal Network Enumeration**
Attackers query internal domains through the forwarder.

Example:
`dig @dns-server internal-db.company.local`

If resolved, attackers discover internal infrastructure such as:
- databases
- VPN servers
- Kubernetes services
- internal APIs

**2. DNS Amplification (DDoS)**
Attackers send **spoofed DNS requests** through the forwarder.
`Attacker → spoofed request → forwarder → large response → victim`
Because DNS responses are larger than requests, this amplifies traffic.

**3. DNS Filtering Bypass**
If a network forces users to use internal DNS, attackers or malware can:
`Client → forwarder → external DNS`
This bypasses domain filtering and allows communication with blocked domains.

**4. Data Exfiltration**
Attackers encode data in DNS queries.

Example:
`sensitive-data.attacker.com`
The forwarder forwards the query to the attacker-controlled DNS server.

##### Root Causes
Common misconfigurations:
- Open recursion (`allow-recursion: any`)
- DNS accessible from the internet
- Forwarding internal zones
- No firewall restrictions on port **53**
## 3. DNS Split-Horizon Bypass
Split DNS = the **same domain resolves differently depending on location**

*Example:*
`From internet:`
`admin.example.com → public IP`

`From inside server:`
`admin.example.com → 10.0.0.5`

*Core idea:*
> _DNS gives different answers based on who is asking_

## 4. DNS Glue Record Abuse
### How to abuse glue records:
#### 1. Glue record poisoning
#### 2. Dangling glue records
###### Situation:
`Domain has:`
`ns1.old-provider.com → 1.2.3.4 (GLUE)`

But:
- server no longer exists
- IP becomes available

**Attack:**
1. Attacker acquires that IP
2. Runs malicious DNS server
3. Now controls resolution for domain
*Similar to subdomain takeover, but at NS level (more powerful)*
#### 3. Partial control / inconsistent glue
**You have:**
`example.com → NS → ns1.example.com`

Now resolver needs IP of `ns1.example.com`.
There are **two possible sources**:

###### Source A — Glue (from parent zone)
`ns1.example.com → 1.2.3.4`
###### Source B — Real DNS lookup
`ns1.example.com → 5.6.7.8`

**Important:**
These **should match**. If they don’t → something is wrong

Now the broken case:

|Source|IP|
|---|---|
|Glue record|1.2.3.4|
|Actual DNS|5.6.7.8|

**Why this happens (real-world reasons)**
- company changed hosting but didn’t update glue
- migration between DNS providers
- misconfiguration
- forgotten old infrastructure

This is **very common**

Which IP does the resolver use?
Answer:  **It depends**

**Different resolvers behave differently:**
###### Resolver A
Uses glue directly:
→ connects to 1.2.3.4
###### Resolver B
Ignores glue, resolves properly:
→ connects to 5.6.7.8
###### Resolver C (worst case)
- tries both
- caches one randomly

Where the vulnerability appears
**Now imagine:**
1.2.3.4 = attacker-controlled  
5.6.7.8 = real server
##### What happens?
**Some users (or systems) will:**
example.com → attacker DNS

**Others:**
example.com → real DNS
## 5. Dangling NS Record Exploitation
### What does “dangling” mean?
“Dangling” = **something points to a resource that no longer exists**. So a **Dangling NS record** means:
> The domain still points to a nameserver that is no longer controlled by the owner.

#### How does this happen?
Very common real-world mistake:
1. Company uses a DNS provider (like `AWS Route53`, `DigitalOcean`, etc.)
2. Their NS records point to:
    ns1.old-dns.com  
    ns2.old-dns.com
3. They migrate to a new provider
4. They **delete the old DNS account or zone**
5. BUT they **forget to update/remove NS records at the registrar**

Now:
- The NS record still points to `ns1.old-dns.com`
- But **no one owns/configured that zone anymore**

#### Where is the exploitation?

Now attacker comes in:
##### Step-by-step attack
1. Attacker finds a domain with NS records pointing to an unused service
2. Attacker checks:
    - Can I register or claim `ns1.old-dns.com`?
    - Or recreate the zone in that provider?
3. If YES:
    - Attacker creates a DNS zone for `example.com`
    - Adds records like:
        
        example.com → A → attacker IP  
        login.example.com → attacker server
        
4. Since NS still points there:
    - DNS resolvers trust the attacker-controlled nameserver

The attacker now controls **DNS resolution for the domain**
## 6. DNS Lame Delegation Exploitation
### What is “lame delegation”?
A nameserver is listed in NS records, **but it is not authoritative for the domain**

So:
`example.com → NS → ns1.example.com`

But when you query `ns1`:
- It says:
    - “I don’t know this domain”
    - or returns **REFUSED / SERVFAIL**
    - or gives wrong zone

That NS is **lame**

#### Why does this happen?

Common mistakes:
##### Case 1 — Migration gone wrong
- Domain moved to new DNS provider
- Old NS still listed
- Old NS no longer has the zone
##### Case 2 — Misconfiguration
- NS exists but zone not loaded
- Wrong config on DNS server
##### Case 3 — Partial deletion
- One NS updated, others not
#### So where is the “exploitation”?

Here’s the honest part:
> Lame delegation is **not a direct takeover vulnerability** like dangling NS.

It’s more like:
> A **weakness in DNS reliability** that can sometimes be abused

#### 5. What actually happens during resolution
Resolvers behave like this:
1. Try one NS
2. If it fails → try another
3. Cache working ones
4. Ignore broken ones over time

So if you have:
example.com → NS → ns1 (working)  
example.com → NS → ns2 (lame)

Most of the time:
- Resolver just ignores `ns2`
## DNS misconfigurations defenses
#### 1. Disable Open Resolvers
DNS servers should not allow recursion for the whole internet.
Only allow trusted IP ranges.
#### 2. Response Rate Limiting (RRL)
Limit how many responses are sent per IP per second.
#### 3. Source IP Filtering
ISPs should block spoofed packets.
But not all ISPs enforce it.
#### 4. Avoid “ANY” Queries
Many modern DNS servers restrict or minimize ANY responses.
#### 5. Use TCP fallback carefully
Some DNS implementations restrict large UDP responses.