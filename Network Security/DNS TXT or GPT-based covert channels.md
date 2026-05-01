### Definition

A **covert channel** that hides data inside DNS **TXT records** to secretly exchange information between a compromised host and an attacker.

The DNS protocol allows arbitrary text in TXT records, which makes them useful for hiding data.

---

# 1️⃣ Why TXT Records Are Abused

DNS TXT records were designed to store **free-form text data**.

Example:

example.com TXT "v=spf1 include:_spf.google.com ~all"

Because TXT records can contain long strings, attackers can place **encoded data inside them**.

---

# 2️⃣ Basic Mechanism

### Step 1 — Victim sends DNS query

The infected machine queries a domain controlled by the attacker:

data1.attacker.com

### Step 2 — Attacker’s DNS server responds with TXT record

Example response:

TXT "Y21kPWRvd25sb2FkIGZpbGU="

The victim decodes the data.

Example decoded:

cmd=download file

This creates a **command-and-control channel**.

---

# 3️⃣ Attack Flow

Victim malware  
     │  
     │ DNS query  
     ▼  
Attacker DNS server  
     │  
     │ TXT response (encoded data)  
     ▼  
Victim executes command

The attacker can:

- send commands
    
- exfiltrate data
    
- maintain persistence
    

All over DNS.

---

# 4️⃣ Why Attackers Use TXT Records

TXT records allow:

- **large payloads**
    
- **arbitrary text**
    
- **multiple strings in one response**
    

Example:

TXT "chunk1" "chunk2" "chunk3"

This makes them ideal for covert communication.

---

# 5️⃣ GPT-Based Covert Channels

“GPT-based” in this context usually means **AI-generated encoded payloads** used to disguise data patterns.

Attackers may use AI models to:

- generate **natural-looking text**
    
- avoid entropy-based detection
    
- encode commands in human-like sentences
    

Example:

TXT "Please remember to check the weather tomorrow."

Hidden encoding could represent a command when processed by malware.

This helps bypass detection systems that look for **random or high-entropy strings**.

---

# 6️⃣ Comparison with DNS Tunneling

|Technique|Where Data Is Stored|
|---|---|
|DNS tunneling|Subdomain in query|
|TXT covert channel|DNS response TXT record|

Example:

DNS tunneling:

dG9rZW4xMjM.attacker.com

TXT covert channel:

TXT "dG9rZW4xMjM="

Both abuse DNS, but **different parts of the protocol**.

---

# 7️⃣ Detection Indicators

Security teams often monitor:

- excessive **TXT queries**
    
- unusual TXT record sizes
    
- frequent DNS communication with same domain
    
- high-entropy TXT responses
    

---

# 8️⃣ Defensive Measures

Mitigation strategies:

- restrict external DNS resolvers
    
- monitor TXT record usage
    
- DNS traffic analysis
    
- block suspicious domains
    
- use DNS security gateways
    

---

# Key Idea

DNS TXT covert channels exploit the fact that:

> DNS TXT records can store arbitrary text, making them useful for hiding commands or exfiltrated data.

They are commonly used in **DNS-based command-and-control systems**.