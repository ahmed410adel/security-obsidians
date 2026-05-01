## 1. Target Scoping #High/Priority
### Input (start here) :
#### 1. Link or Text file or Image
#### 2. Username & Password (better create two accounts)
#### 3. Account handle (hacker one, etc.....)
#### 4. Domain name
### Output :
#### 1. in scope domains or IPs
#### 2. in scope subdomains
#### 3. out of scope domains IPs
#### 4. out of scope subdomains
#### 5. Needed headers or emails
#### 6. Scanner speed
#### 7. Questions to answer
- Can I test domains and subdomains not listed in the program rules ?
- Can I test acquisitioned websites ?
- Does this site provide premium account for testing ?

| Number | Task                                                              | AI or Code | Priority         |
| ------ | ----------------------------------------------------------------- | ---------- | ---------------- |
| 1      | List in scope and out of scope domains and IPs                    | AI         | #High/Priority   |
| 2      | List in scope and out of scope subdomains and IPs                 | AI         | #High/Priority   |
| 3      | List in scope and out of scope vulnerabilities                    | AI         | #High/Priority   |
| 4      | Is there any header I should add                                  | AI         | #High/Priority   |
| 5      | Is there an email I should use                                    | AI         | #High/Priority   |
| 6      | Can I use scanner and if I can is there a rule about speed ?      | AI         | #High/Priority   |
| 7      | Take input as link                                                | AI         | #High/Priority   |
| 8      | Can I test domains and subdomains not listed in the program rules | AI         | #High/Priority   |
| 9      | Take input as text file                                           | AI         | #Medium/Priority |
| 10     | Take input as image                                               | AI         | #Low/Priority    |
| 11     | Can I test acquisitioned websites ?                               | AI         | #Low/Priority    |
| 12     | Does this site provide premium account for testing ?              | AI         | #Low/Priority    |
| 13     | Get the acquisitioned Websites                                    | Code       | #Low/Priority    |

## 2. Collecting Other Domains #Low/Priority
A company might own many root domains beyond what is explicitly listed in the bug bounty program ? but why ?
- **Business reasons**: Different TLDs for different regions or markets. (`*.cn`, `*.sa`, `*.ja`)
- **Brand protection**: Companies buy domains defensively to stop phishing. (`nnicrosoft`)
- **M&A (Mergers & Acquisitions)**: They acquire another company with different domains.
- **Legacy**: Old domains still resolve, but they’re not actively used.
- **Non-production assets**: Internal projects, test domains, etc.
### Inputs :
#### 1. Domain names

| Number | Task                                                 | AI or Code | Priority       |
| ------ | ---------------------------------------------------- | ---------- | -------------- |
|        | Use `Nslookup` on domain names to get IP             | Code       | #High/Priority |
|        | use `http://ip-api.com/json/20.140.48.70` to get ASN | Code       | #High/Priority |
## 3. Subdomain Enumeration #High/Priority
### Input :
#### 1. Domain names
### Output :
#### 1. Subdomains
---
##### Working passively
**APIs we will use (free):**
- [x] `crt.sh` #High/Priority
- [x] `commoncurl` #Medium/Priority
- [x] `waybackarchive` #Medium/Priority
- [x] `threatcrowd` #Medium/Priority
##### Working actively #Low/Priority
## 4. Alive Hosts #High/Priority
### Input :
#### 1. Subdomains
### Output :
#### 1. Subdomains
## 5. Visual Proof #Medium/Priority
### Input :
#### 1. Subdomains
### Output :
#### 1. Screenshots
get screenshots for every subdomain and pass it to AI to analyse it #Medium/Priority
## 6. Directory Fuzzing #High/Priority
### Input :
#### 1. Domains
### Output :
#### 1. Endpoints
## 7. Analyze JS Files #Low/Priority
### Input :
#### 1. JS Files
### Output :
#### 1. Information Or Bugs
## 8. Crawling & Spidering #High/Priority 
### Input :
#### 1. Domains
### Output :
#### 1. Log

###### Stored Information
- Request Number
- Host
- Method
- URL
- Params
- Status Code
- Response Content Length int64
- MIME Type
- Extension
- IP
- Cookies
- Response Time
- Request Content
- Response Content
- Request Header
- Response Header


## 9. Look For Hidden Parameters #Low/Priority 
## 10. Network Scanning #Low/Priority
## 11. Public-Source Leaks & Credentials #Medium/Priority
## 12. Technology & Fingerprinting #Medium/Priority

---
