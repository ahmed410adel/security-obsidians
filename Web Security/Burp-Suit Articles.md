# 2025
## 1. The Fragile Lock: Novel Bypasses For SAML Authentication (Security Assertion Markup Language)

#### XML Signature Wrapping Attack (XSW)
The overall security of this flow depends entirely on how the SAML Response signature is validated. In many implementations, signature verification and assertion processing are handled by separate modules or even different XML parsers. An XML Signature Wrapping (XSW) attack exploits the discrepancies between these components.

In a typical scenario, an attacker intercepts a legitimate SAML Response signed by a trusted Identity Provider and injects a new malicious Assertion containing arbitrary user information into the same document. When the Service Provider processes the response, the signature verification module correctly validates the legitimate portion of the message, while the SAML processing logic mistakenly consumes the attacker’s injected Assertion. As a result, the attacker’s forged data is treated as authentic, leading to a privilege escalation.

Juraj Somorovsky, in his research "On Breaking SAML: Be Whoever You Want to Be" suggests that this could be done by registering through the IdP, performing a man-in-the-middle attack, or even digging through publicly exposed files using Google dorking. The problem is that this is a big requirement. Getting a valid signed SAML Assertion for an arbitrary website is extremely difficult. Identity Providers almost never expose them, and even if you somehow capture one, most Service Providers will accept it only once, after that it gets cached and rejected.
![[Pasted image 20260126223934.png]]


# 2024
## 1. Bypassing WAFs with the phantom $Version cookie
![[a237-article-64488e2d-1d5d-45d1-92e2-395fd491f11a.png]]
cookie parsing has a messy history because of multiple competing standards:
- **RFC2109** (old, supports `$Version`, `$Path`, `$Domain`).
- **RFC6265** (modern, simplified).
Different frameworks still support old rules
An attacker can force a server into legacy parsing mode (`$Version=1`) and then abuse quirks
#### Bypassing techniques
##### 1. Escaping and Injection in Cookie Values
Cookies allow quoted values:
- Octal encoding in Python Simple Cookie
`"\073" → ;`
`"\012" → newline`
- Backslash escaping in Tomcat
`foo="\b\a\r" → becomes foo="bar"`
##### 2. WAF Bypass Techniques
- Quoted-string encoding
`"\e\v\a\l\(\'\t\e\s\t\'\)" → eval('test')`
`"\145\166\141\154\050\047\164\145\163\164\047\051" → octal → eval('test')`
- RFC2109 allows commas between cookies 
`$Version=1; foo=bar, abc=qux`
- Also, spaces/tabs around `=` are valid:
`abc    =   qux`
- Multiple Cookie headers
`Cookie: param1=value1;`
`Cookie: param2=value2;`
- Multiline continuation
`Cookie: name=eval('test//`
`Cookie: comment')`
##### 3. Defences
- Disable legacy RFC2109 support (`$Version`, `$Path`, `$Domain`) unless 100% needed.
- Normalize cookies before processing.
- Validate input strictly (don’t trust “quoted safe strings”).
- Don’t rely on simple blocklists → use context-aware filtering.

---
# 2020
## Portable Data exFiltration: XSS for PDFs
![[6df2-article-cross-site-scripting-for-pdfs_article_copy_4.jpg]]






















# 1. Document My Pentest: you hack, the AI writes it up!
![[369e-article-article.png]]
