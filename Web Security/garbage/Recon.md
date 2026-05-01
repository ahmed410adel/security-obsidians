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