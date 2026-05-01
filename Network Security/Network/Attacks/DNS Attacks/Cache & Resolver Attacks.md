## how DNS resolution works
DNS translates a domain **(example.com) → IP address**

Actors involved:
1. **Client (browser)** – asks for the IP.
2. **Recursive resolver** – the DNS server your ISP/company uses.
3. **Authoritative DNS server** – the real owner of the domain.

Typical flow:
**Browser → Recursive Resolver → Root → TLD (.com) → Authoritative → IP**
To make this faster, the **recursive resolver caches responses**.
Example:

example.com → 93.184.216.34  
TTL = 3600 seconds

For the next hour, the resolver answers from cache without asking upstream.

## What DNS cache poisoning means

**DNS cache poisoning = inserting a fake DNS record into the resolver cache.**

If successful:
bank.com → attacker-IP

Every user using that resolver gets redirected. The key target is **the recursive resolver cache**, not the client.

### Why poisoning was historically possible

When a resolver asks:

**What is the IP of example.com?**
It sends a **DNS query with a transaction ID**.

Example:
`TXID = 0x1a2b`
`Source port = 33333`  
`Query = example.com`

The resolver accepts the **first response that matches**:
TXID matches  
source IP looks correct

If an attacker guesses the TXID before the real server responds → the fake answer is cached.

### The original weakness
Old DNS resolvers used:

TXID: 16 bits 
source port: fixed (often 53)

**So attackers only had to guess:**
65,536 possibilities not hard. But the attacker must win the **race condition** attacker response arrives before legitimate response


---
## 1. DNS Cache Poisoning Kaminsky Technique
Instead of attacking `example.com` attack **random subdomains**:
`aaaa.example.com`
`bbbb.example.com` 
`cccc.example.com`

These **do not exist in cache**, forcing the resolver to ask upstream. Every request creates a **new race opportunity** and the attacker sends **many forged responses**, not just one for every query


The attacker does **not poison the random subdomain**. Instead they poison the **nameserver delegation**. Example forged response:

**Question:**  
`abcd.example.com`
  
`Answer:`  
`NXDOMAIN`  
  
`Authority section:`  
`example.com NS ns.attacker.com`

**The resolver caches:**
example.com → ns.attacker.com

Now the attacker controls **all subdomains**:
`login.example.com`  
`mail.example.com`  
`api.example.com`

## 2. DNS Cache Poisoning via Fragmentation
![[Pasted image 20260307153351.png]]

#### why DNS responses can be fragmented
DNS usually uses **UDP**. UDP has no built-in reliability and packets have size limits. Typical limits:
*Ethernet MTU ≈ 1500 bytes*

Large DNS responses (DNSSEC, many records, long TXT records) can exceed this. Example:
*DNS response = 3000 bytes*

**The IP layer splits it:**
`Fragment 1`  
`Fragment 2`  
`Fragment 3`

**Each fragment has fields like:**
`IP ID`  
`Fragment offset`  
`More fragments flag`

The receiver **reassembles them** using the **IP ID**. Resolver receives fragments like this:
Fragment 1  
Fragment 2  
Fragment 3

When all arrive, the OS reconstructs the packet. Complete DNS response. Then the resolver processes it. The **TXID and DNS data are not always in the first fragment**.

#### The key weakness
The operating system reassembles fragments **before DNS checks anything**. So the OS only verifies:
`same IP ID`  
`same source IP`  
`same destination IP`  
`same protocol`

If those match, fragments are combined. This means fragments from different sources can be mixed
#### The attacker model
This attack is usually **off-path**. Meaning attacker cannot see DNS traffic. But they can **send spoofed packets**.

Goal:
Inject a **fake fragment** that will be combined with a legitimate fragment. also attacker can know the offset size by ping and test the scenario
If **fragment 1 already contains the real answer**, then poisoning fails. So attackers design the situation so that **fragment 1 does NOT contain the final answer**.
## 3. DNS Cache Poisoning via Glue Records
#### why glue records exist
DNS is hierarchical. When a resolver asks the root servers about `example.com`, the root does **not** give the final IP. It gives a **delegation** to the `.com` nameservers.
##### Example:
**Query:**  
`A example.com`

**Root response:**  
`Authority:`
`example.com NS ns1.example.com`  
`example.com NS ns2.example.com`

Problem:  
To reach `ns1.example.com`, the resolver must know its IP. But the IP itself may require another DNS lookup. This creates a **dependency loop**. To break this loop, DNS uses **glue records**. Glue records are **extra A records included in the response** that tell the resolver the IP of the nameserver.

##### Example:
`Authority section:`  
`example.com NS ns1.example.com`  
  
`Additional section (Glue):`  
`ns1.example.com A 192.0.2.10`

Now the resolver knows where to send the next query.

Resolvers often **cache glue records** because they look like legitimate A records. So the resolver might store:
`ns1.example.com → 192.0.2.10`
Even though the resolver **never asked directly** for that A record. This is the weakness. Resolvers historically **trusted glue records too much**. An attacker can inject **fake glue records** into the resolver cache. If the resolver accepts them, future queries are redirected.
## 4. DNS Cache Poisoning via Additional Section Injection
DNS responses contain four sections:
1. **Question** – the original query
2. **Answer** – the direct response
3. **Authority** – authoritative name servers
4. **Additional** – extra records (usually IPs of nameservers) included to avoid extra lookups

Some DNS resolvers historically **cached records from the Additional section without strict validation**. This allowed attackers to insert **unrelated DNS records** that the resolver would store in its cache.

#### Attack Idea
The attacker sends a DNS response that contains **legitimate data for the requested domain** but also includes **malicious records in the Additional section** for other domains. If the resolver trusts and caches those records, future requests for those domains will resolve to the attacker.
##### Example
**Resolver queries:**
```
evil-domain.com A ?
```

**Attacker-controlled DNS responds:**
```
Answer:
evil-domain.com A 1.2.3.4

Additional:
bank.com A 6.6.6.6
paypal.com A 6.6.6.6
```

If the resolver caches these records:
```
bank.com → 6.6.6.6
paypal.com → 6.6.6.6
```

Users querying the resolver for those domains are redirected to the attacker.

#### Why It Works
Some resolvers previously trusted records in the **Additional section** even when they were **unrelated to the original query**. This allowed attackers to inject arbitrary domain records into the cache.
#### Impact

- Redirection of users to attacker-controlled servers
- Phishing or credential theft
- Traffic interception
- Man-in-the-middle attacks
These protections make Additional Section Injection **rare in modern resolvers**.
## 5. DNS Cache Poisoning via CNAME chaining
#### DNS Cache Poisoning via CNAME Chaining
A **CNAME (Canonical Name) record** maps one domain to another domain.  
When a resolver receives a CNAME, it must **resolve the target domain next**.

Example:
```
login.example.com CNAME auth.example.net
```

Resolver process:
1. Query `login.example.com`
2. Receive CNAME → `auth.example.net`
3. Query `auth.example.net`
4. Return the final IP

Resolvers usually **cache every step** in this chain.
#### Attack Idea
If an attacker controls a domain inside the chain, they may try to **inject additional DNS records during the resolution process**, causing the resolver to cache malicious entries. The attacker abuses the fact that the resolver **follows the CNAME chain automatically** and may trust records returned alongside the response.


#### Example Attack Flow
**Resolver queries:**
```
victim.com A ?
```

**Attacker manipulates delegation so the response becomes:**
```
Answer:
victim.com CNAME attacker-domain.com
```

**Resolver now queries:**
```
attacker-domain.com A ?
```

**Attacker-controlled DNS responds:**
```
Answer:
attacker-domain.com A 1.2.3.4

Additional:
bank.com A 6.6.6.6
```

If the resolver accepts the additional records, the cache becomes:
```
bank.com → 6.6.6.6
```

Users querying `bank.com` through that resolver are redirected to the attacker.

#### Why CNAME Chaining Helps the Attacker
CNAME chains:
- force the resolver to perform **multiple DNS lookups**
- allow the attacker to **control part of the resolution path**
- create opportunities to deliver **malicious additional records**

This can increase the chance of successful cache poisoning.
## 6. DNS Negative Cache Poisoning
DNS resolvers cache not only valid answers but also **negative responses**.
A negative response means the domain **does not exist** or **has no record**.

Example:
```
nonexistent.example.com A ?
```

Response:
```
RCODE: NXDOMAIN
```

The resolver stores this result in its **negative cache** so it doesn't repeatedly query the authoritative server for the same non-existent domain. This behavior is defined in **RFC 2308**.
#### What Gets Cached
Resolvers may cache two types of negative responses:
1. **NXDOMAIN** – the domain does not exist.
2. **NODATA** – the domain exists but the requested record type does not.

Example:
```
example.com AAAA ?
```

If `example.com` only has an A record:

```
NOERROR
Answer: (empty)
```

This is cached as **NODATA**.
#### Attack Idea
If an attacker can make a resolver cache a **fake negative response**, the resolver will believe a legitimate domain **does not exist**. During the cache lifetime, users cannot resolve that domain. This is essentially **DNS denial-of-service via poisoning**.
## 7. SAD DNS Attack (Side-channel Attack on DNS)
The **SAD DNS attack** is a modern technique that revives classic **DNS cache poisoning** by bypassing a key defense: **source port randomization**. Traditional DNS poisoning requires guessing two values in a resolver’s query:
- **Transaction ID (16 bits)**
- **UDP source port (≈16 bits)**

Together they create ~32 bits of entropy, making brute-force poisoning very difficult.
The **SAD DNS attack** reduces this entropy using a **side-channel leak** in the network stack.

#### Key Idea
Resolvers send DNS queries over **UDP**. Attackers normally cannot see the source port used by the resolver. However, SAD DNS uses **ICMP rate limiting behavior** in operating systems to **infer which UDP port is in use**.

Once the attacker learns the port, they only need to guess the **transaction ID**, making cache poisoning feasible again.

#### Attack Overview
1. **Trigger a DNS query**
The attacker causes the resolver to request a domain they control.
##### Example:

```id="z7p3kq"
random123.victim.com A ?
```

2. **Port scanning via side channel**
The attacker sends many spoofed UDP packets toward the resolver, pretending to come from the authoritative DNS server.

Example packets:
```id="m3l0sd"
spoofed source: authoritative DNS
destination: resolver
UDP port: 1..65535
```

If the port is **closed**, the resolver generates an **ICMP "port unreachable"** reply. Operating systems **rate-limit ICMP responses**. By measuring when ICMP responses stop appearing, the attacker can infer **which UDP port is open**.

3. **Determine the resolver's DNS port**
Using the ICMP behavior, the attacker learns:
```id="94vzy9"
resolver DNS query source port = 52341
```
This breaks the **source port randomization protection**.

4. **Send forged DNS responses**
Now the attacker floods forged responses with different transaction IDs:

```id="0c83z3"
victim.com A 6.6.6.6
```

Since the correct port is known, only **65k transaction IDs** need to be guessed.
Eventually one response matches:

```
(transaction ID + port)
```

The resolver accepts it and caches the poisoned record.

#### Why SAD DNS Works
The attack abuses **side-channel information leakage**. The resolver does not directly reveal its port, but the **ICMP rate limiting behavior indirectly leaks it**. So the attacker converts a **blind guessing problem** into a **detectable signal**.

The attack works **without being on the same network**.

*Linux kernel patches (2020+) specifically addressed this side channel.*

SAD DNS does **not directly poison DNS**. Instead, it **reveals the resolver’s UDP source port via a side-channel**, making classic **DNS cache poisoning attacks feasible again**.
## 8. NXNS Attack (Name Server Delegation Attack)
The **NXNS attack** abuses how DNS resolvers process **NS (Name Server) delegations**. Normally, when a resolver queries a domain, the authoritative server may return **NS records** that delegate the query to other name servers. Resolvers then contact those name servers to continue resolution. The NXNS attack manipulates this process so that the resolver is forced to make **a very large number of recursive DNS queries**, overwhelming the resolver or the target servers.

#### Normal DNS Delegation

Example query:
```id="g71v2h"
attacker.example A ?
```

Response from authoritative server:
```id="5x3c2n"
Authority:
attacker.example NS ns1.attacker.com
attacker.example NS ns2.attacker.com
```

Resolver then queries:
```id="l9y4ak"
ns1.attacker.com
ns2.attacker.com
```

to get the final IP.

#### Attack Idea
The attacker returns a **large number of NS records**, each pointing to **different domains that require further resolution**.

Example malicious response:
```id="4kzj31"
Authority:
victim.com NS ns1.attacker.com
victim.com NS ns2.attacker.com
victim.com NS ns3.attacker.com
...
victim.com NS ns1000.attacker.com
```

Each nameserver requires an IP lookup:
```id="5rt12j"
ns1.attacker.com A ?
ns2.attacker.com A ?
ns3.attacker.com A ?
...
```

The resolver suddenly generates **hundreds or thousands of DNS queries**.

#### Amplification Effect
One initial query can trigger **many recursive queries**.

Example:
```id="fy3q0l"
1 query → resolver
resolver performs → 1000 DNS queries
```

This creates a **query amplification attack**. The attacker can cause:
- resolver CPU exhaustion
- resolver memory exhaustion
- DNS traffic amplification
- denial-of-service against other DNS servers

#### Why It Works
Resolvers traditionally trusted NS delegations and attempted to resolve **all listed name servers**. If those nameservers belonged to domains requiring further lookups, the resolver recursively chased each one. Attackers exploit this behavior to create **query explosions**.

#### Impact
NXNS attacks can cause:
- DNS resolver denial of service
- amplification of DNS traffic
- cascading failures in DNS infrastructure
- overload of authoritative servers
Unlike cache poisoning, NXNS is primarily a **resource exhaustion attack**.

#### Modern Mitigations
Resolvers now limit this behavior by:
- limiting the number of nameservers processed
- limiting recursive queries per request
- validating delegation chains
- rate limiting recursive resolution

Major DNS software (BIND, Unbound, PowerDNS) patched this around **2020**.

#### Key Idea
NXNS does **not poison DNS records**. Instead, it **forces resolvers to perform excessive recursive lookups by abusing nameserver delegation**, leading to DNS infrastructure denial-of-service.










## DNS cache poison defenses
### 1. Source port randomization
**Instead of fixed port 53:**
TXID = 16 bits  
Port = 16 bits

**Entropy:**
32 bits  
≈ 4 billion combinations

Much harder to brute-force.
### 2. DNS 0x20 encoding
Random capitalization:
`Example.com`
`eXaMpLe.CoM`

Authoritative server must echo exact case. Adds entropy.
### 3. DNSSEC
DNS responses are **cryptographically signed**.
### 4. Bailiwick checking (defend from glue records attack)
Resolvers only accept glue records **within the correct authority**.
Example allowed:
`example.com NS ns1.example.com`  
`ns1.example.com A 192.0.2.10`

Example rejected:
`example.com NS ns1.example.com`  
`ns1.google.com A 1.2.3.4`

Because it is **outside the domain authority**.
### 5. strict validation of additional records
### 6. CNAME chain limits
### 7. randomizing ICMP rate limits (defend from SAD)
### 8. filtering ICMP responses (defend from SAD)
### 9. improving resolver port randomization (defend from SAD)

#### Attack impact:
- phishing
- malware delivery
- traffic interception
- full domain hijacking