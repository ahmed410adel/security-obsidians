Web cache deception is a vulnerability that enables an attacker to trick a web cache into storing sensitive, dynamic content. It's caused by discrepancies between how the cache server and origin server handle requests
In a web cache deception attack, an attacker persuades a victim to visit a malicious URL, inducing the victim's browser to make an ambiguous request for sensitive content. The cache misinterprets this as a request for a static resource and stores the response. The attacker can then request the same URL to access the cached response, gaining unauthorized access to private information

![[wcd-image-1.png]]

## What is the difference between cache poisoning attack and cache deception attack ?
- Web cache poisoning manipulates cache keys to inject malicious content into a cached response, which is then served to other users
- Web cache deception exploits cache rules to trick the cache into storing sensitive or private content, which the attacker can then access

## Cache rules
- **Static file extension rules** - These rules match the file extension of the requested resource, for example `.css` for stylesheets or `.js` for JavaScript files.
- **Static directory rules** - These rules match all URL paths that start with a specific prefix for example `/static` or `/assets`.
- **File name rules** - These rules match specific file names to target files that are universally required for web operations and change rarely, such as `robots.txt` and `favicon.ico`.
*Caches may also implement custom rules based on other criteria, such as URL parameters or dynamic analysis*

## Constructing a web cache deception attack
1. Identify a target endpoint that returns a dynamic response containing sensitive information
2. Identify a discrepancy in how the cache and origin server parse the URL path
3. Craft a malicious URL that uses the discrepancy to trick the cache into storing a dynamic response

## Detecting cached responses
from `X-Cache` header
- `X-Cache: hit` - The response was served from the cache.
- `X-Cache: miss` - it was fetched from the origin server
- `X-Cache: dynamic` - The origin server dynamically generated the content. Generally this means the response is not suitable for caching.
- `X-Cache: refresh` - The cached content was outdated and needed to be refreshed or revalidated.
- The `Cache-Control` header may include a directive that indicates caching, like `public` with a `max-age` higher than `0`. Note that this only suggests that the resource is cacheable. It isn't always indicative of caching, as the cache may sometimes override this header
- If you notice a big difference in response time for the same request, this may also indicate that the faster response is served from the cache.

## Delimiter discrepancies
Consider the example `/profile;foo.css`:
- The Java Spring framework uses the `;` character to add parameters known as matrix variables. An origin server that uses Java Spring would therefore interpret `;` as a delimiter. It truncates the path after `/profile` and returns profile information.
- Most other frameworks don't use `;` as a delimiter. Therefore, a cache that doesn't use Java Spring is likely to interpret `;` and everything after it as part of the path

All delimiters
``! " # $ % & ' ( ) * + , - . / : ; < = > ? @ [ \ ] ^ _ ` { | } ~ %21 %22 %23 %24 %25 %26 %27 %28 %29 %2A %2B %2C %2D %2E %2F %3A %3B %3C %3D %3E %3F %40 %5B %5C %5D %5E %5F %60 %7B %7C %7D %7E``

## Labs
* #Portswigger
	1. **Exploiting path mapping for web cache deception** #APPRENTICE 
		I tried to cache my account page by adding this endpoint `/adel.css` and it worker so i create a script and deliver it to the victim to cache his account page and get his API key
		*script :*
		`<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account/adel.js"</script>`
	2. **Exploiting path delimiters for web cache deception** #PRACTITIONER 
		I tried to cache my account page by adding this delimiter `;adel.js` and it worker so I create a script and deliver it to the victim to cache his account page and get his API key
		*script :*
		`<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account;adel.js"</script>`
	3. **Exploiting origin server normalization for web cache deception** #PRACTITIONER 
		in this lab the cache based on path so after looked for where the path of static files we got `/resources` so we tried to add this as end point `/resources/..%2fmy-account` and we got 200 ok so I write the script of it and delivered it to the victim
		*script :*
		`https://YOUR-LAB-ID.web-security-academy.net/resources/..%2fmy-account?wcd`
	4. **Exploiting cache server normalization for web cache deception** #PRACTITIONER 
		this lab cache `/resources` and accepts these delimiters `# %2F %23 ?`
		*script :*
		`https://YOUR-LAB-ID.web-security-academy.net/my-account%23%2f%2e%2e%2fresources?wcd`
	5. **Exploiting exact-match cache rules for web cache deception** #EXPERT 


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
    score: 70
  - item: "Client-Side Code"
    user: "Effective against the attack"
    score: 0
  - item: "WAF"
    user: "Effective against the attack"
    score: 40

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