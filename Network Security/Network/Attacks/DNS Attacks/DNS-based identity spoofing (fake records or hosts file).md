## Method 1 — Fake DNS Records
This is network-level manipulation.
### How it works
An attacker manipulates DNS responses so that:
victim.com → attacker IP
instead of the legitimate IP.

This can happen via:
- DNS poisoning
- Rogue DNS server
- Compromised router
- DNS cache poisoning
- DHCP manipulation

## Method 2 — Hosts File Manipulation
This is local-level manipulation.
Every OS has a **hosts file**.
It overrides DNS.

### Windows:
`C:\Windows\System32\drivers\etc\hosts`
### Linux/macOS:
`/etc/hosts`

Format:
IP_ADDRESS   domain name

Example:
127.0.0.1    facebook.com

Now, whenever you type facebook.com, your system goes to localhost.
*DNS is never even queried.*