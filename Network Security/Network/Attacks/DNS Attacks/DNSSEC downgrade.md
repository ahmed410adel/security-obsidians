### Definition

An attack where the attacker tries to make a **DNSSEC-protected domain be treated as unsigned**, allowing forged DNS responses to be accepted.

Instead of breaking cryptography, the attacker tries to **disable or bypass DNSSEC validation**.

---

# DNSSEC Basics

DNSSEC adds **cryptographic signatures** to DNS records to guarantee authenticity.

Important records:

- **DNSKEY** → public key of the zone
    
- **RRSIG** → signature of DNS records
    
- **DS record** → link between parent and child zone
    

Chain of trust:

Root → TLD (.com) → domain

A **validating resolver** verifies these signatures before accepting the response.

If validation fails → resolver returns **SERVFAIL**.

---

# Goal of the Attack

The attacker attempts to:

Signed domain → treated as unsigned

If successful, the attacker can:

- inject fake DNS responses
    
- perform cache poisoning
    
- redirect users to malicious servers
    

---

# Downgrade / Bypass Techniques

## 1. DO Bit Manipulation

DNSSEC uses the **DO (DNSSEC OK) bit** in EDNS.

If the attacker removes this bit during transmission:

Resolver → query with DO=1  
Attacker → strips DO bit  
Authoritative server → returns unsigned response

If the resolver accepts this response, DNSSEC validation is bypassed.

---

## 2. Truncation Attack (UDP Fragmentation)

DNSSEC responses are large.

Attackers may:

- drop fragmented packets
    
- force response truncation
    
- interfere with TCP fallback
    

Goal: make resolver retry without DNSSEC.

---

## 3. Broken Chain of Trust

DNSSEC requires a continuous chain:

Parent zone → DS record → child zone DNSKEY

If DS records are missing or misconfigured:

- resolver treats the zone as **unsigned**
    
- attacker may inject fake responses
    

This is a **configuration weakness**.

---

## 4. Resolver Implementation Bugs

Some resolvers historically mishandled:

- wildcard records
    
- CNAME chains
    
- negative responses (NSEC/NSEC3)
    
- mixed signed/unsigned responses
    

Attackers could exploit these logic errors to bypass validation.

---

## 5. Fail-Open Behavior

Correct behavior:

Validation failure → SERVFAIL

Broken behavior:

Validation failure → accept response

This misconfiguration completely defeats DNSSEC.

---

# Why DNSSEC Crypto Is Not Broken

DNSSEC relies on strong cryptography:

- RSA
    
- ECDSA
    
- SHA-256 / SHA-384
    

Real attacks target:

- resolver bugs
    
- DNS misconfiguration
    
- incomplete deployments
    

Not the cryptography itself.

---

# Detection and Defense

Mitigations:

- enable **DNSSEC validation on resolvers**
    
- enforce **fail-closed validation**
    
- monitor **SERVFAIL spikes**
    
- check **AD (Authenticated Data) flag**
    
- secure DNS traffic (DoT / DoH)
    

---

# Pentesting Note

DNSSEC downgrade attacks usually require:

- network-level access (MITM)
    
- resolver misconfiguration
    
- outdated DNS software
    

More common findings include:

- domains without DNSSEC
    
- broken DS records
    
- resolvers that do not validate DNSSEC.
    

---

**Key Idea**

DNSSEC downgrade attacks do not break signatures.  
They attempt to **prevent validation from happening at all**.