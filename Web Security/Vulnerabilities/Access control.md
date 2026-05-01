# What is access control?
Access control is the application of constraints on who or what is authorized to perform actions or access resources. In the context of web applications, access control is dependent on authentication and session management:
- **Authentication** confirms that the user is who they say they are.
- **Session management** identifies which subsequent HTTP requests are being made by that same user.
- **Access control** determines whether the user is allowed to carry out the action that they are attempting to perform.
![[access-control.svg]]
### Vertical access controls
Vertical access controls are mechanisms that restrict access to sensitive functionality to specific types of users.
**for example :**
- if a non-administrative user can gain access to an admin page where they can delete user accounts, then this is vertical privilege escalation

### Horizontal access controls
Horizontal access controls are mechanisms that restrict access to resources to specific users.
**for example :**
- reading another user's data
- taking another user's privilege

### robots.txt
tells search engine crawlers which URLs the crawler can access on your site

---
Some application frameworks support various non-standard HTTP headers that can be used to override the URL in the original request, such as `X-Original-URL` and `X-Rewrite-URL`. If a website uses rigorous front-end controls to restrict access based on the URL, but the application allows the URL to be overridden via a request header, then it might be possible to bypass the access controls using a request like the following:
`POST / HTTP/1.1`
`X-Original-URL: /admin/deleteUser`
`...`

### Referrer-based access control
Some websites base access controls on the `Referer` header submitted in the HTTP request. The `Referer` header can be added to requests by browsers to indicate which page initiated a request.
For example, an application robustly enforces access control over the main administrative page at `/admin`, but for sub-pages such as `/admin/deleteUser` only inspects the `Referer` header. If the `Referer` header contains the main `/admin` URL, then the request is allowed.
In this case, the `Referer` header can be fully controlled by an attacker. This means that they can forge direct requests to sensitive sub-pages by supplying the required `Referer` header, and gain unauthorized access.

### Location-based access control
Some websites enforce access controls based on the user's geographical location. This can apply, for example, to banking applications or media services where state legislation or business restrictions apply. These access controls can often be circumvented by the use of web proxies, VPNs, or manipulation of client-side geolocation mechanisms.

## Labs
* #Portswigger
	1. **Unprotected admin functionality** #APPRENTICE 
		search in the `robot.txt` file you will get the admin panel try to access it
	2. **Unprotected admin functionality with unpredictable URL** #APPRENTICE 
		try to access / and notice the response you will find the admin URL disclose
	3. **User role controlled by request parameter** #APPRENTICE 
		change the parameter `admin=false` to true 
	4. **User role can be modified in user profile** #APPRENTICE 
		you will send your request in a JSON format add `roleid=2` you will be admin
	5.  **User ID controlled by request parameter** #APPRENTICE 
		change the id in the URL to Carlos you will git his API key
	6.  **User ID controlled by request parameter, with unpredictable user IDs** #APPRENTICE 
		you will find GUID of Carlos in the post copy it and change you GUID in the URL to get the API
	7. **User ID controlled by request parameter with data leakage in redirect** #APPRENTICE 
		try to change your ID to Carlos the site will log you out but when you look at the response you will found Carlos API key
	8. **User ID controlled by request parameter with password disclosure** #APPRENTICE 
		change your ID to administrator you will find the password of the administrator
	9. **Insecure direct object references** #APPRENTICE 
		try to download the chat notice that the downloaded chat name `2.txt` try to find the request and change it to `1.txt`
	10. **URL-based access control can be circumvented** #PRACTITIONER 
		we will use `X-Original-URL` header to access `/admin` because it is blocked from accessing so we will circumvent the request but this header do not accept query strings so we will add the our query in the host like this `/?username=carlos`  
	11. **Method-based access control can be circumvented** #PRACTITIONER 
		take the cookies of the administrator and use it access admin page and do the task and then change the request method
	12. **Multi-step process with no access control on one step** #PRACTITIONER 
		take the cookies of the administrator and use it access admin page and do the task
	13. **Referrer-based access control** #PRACTITIONER 
		take the cookies of the administrator and use it access admin page and do the task

# Common defenses against access control vulnerabilities
- Never rely on obfuscation alone for access control.
	`https://example.com/admin1234` (instead of a proper access control mechanism)
- deny access by default.
	Use a principle called **"zero trust"**
- Instead of handling access control logic in multiple places (e.g., in each function or page), use a centralized system
- At the code level, make it mandatory for developers to declare the access that is allowed for each resource, and deny access by default.
- Thoroughly audit and test access controls to ensure they work as designed.
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
# NOTES
- entropy is the degree of randomness in the ID

# Techniques
#### 1. changing the Request method while changing the user ID to get, put, delete, patch, etc
#### 2. 1. Parameter Pollution
`?user_id=<attacker_id>&user_id=<victim_id>`
#### 3. adding special characters to the user ID
`0a% %20 %1c %09 %00`