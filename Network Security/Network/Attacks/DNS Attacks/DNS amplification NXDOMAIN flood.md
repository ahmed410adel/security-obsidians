A **DNS resource exhaustion attack** where attackers send queries for **non-existent domains**, forcing resolvers to perform expensive recursive lookups.

NXDOMAIN = “domain does not exist”.

Example:
`random1.example.com`
`random2.example.com`
`random3.example.com`

## Why It Works
Resolvers must perform recursive queries:

Resolver  
 ├ Root servers  
 ├ TLD servers  
 └ Authoritative servers
 
Random subdomains bypass caching, forcing new lookups every time.

This consumes:
- CPU
- memory
- network bandwidth
- resolver cache resources

## Attack Flow
Botnet → random DNS queries  
Resolver → recursive lookups  
Root/TLD/Authoritative servers

Large volumes can overwhelm the resolver.

## Variant
**DNS Water Torture Attack**

Attackers generate large numbers of random subdomains:
a1.example.com  
b2.example.com  
c3.example.com

Each query forces a full recursive resolution.

## Key Differences

|Attack|Target|Method|
|---|---|---|
|DNS Amplification|Victim system|Reflect large DNS responses|
|NXDOMAIN Flood|DNS resolver|Force expensive recursive lookups|

Amplification → **bandwidth exhaustion**  
NXDOMAIN flood → **resource exhaustion**