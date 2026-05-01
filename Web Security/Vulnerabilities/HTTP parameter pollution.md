## What is HPP ?
HTTP Parameter Pollution (HPP) is a web vulnerability that occurs when an attacker sends multiple parameters with the same name in an HTTP request, leading the application to process them in an unintended way

|Web Application Server Backend|Parsing Result|Example|
|---|---|---|
|ASP.NET / IIS|All occurrences concatenated with a comma|color=red,blue|
|ASP / IIS|All occurrences concatenated with a comma|color=red,blue|
|.NET Core 3.1 / Kestrel|All occurrences concatenated with a comma|color=red,blue|
|.NET 5 / Kestrel|All occurrences concatenated with a comma|color=red,blue|
|PHP / Apache|Last occurrence only|color=blue|
|PHP / Zeus|Last occurrence only|color=blue|
|JSP, Servlet / Apache Tomcat|First occurrence only|color=red|
|JSP, Servlet / Oracle Application Server 10g|First occurrence only|color=red|
|JSP, Servlet / Jetty|First occurrence only|color=red|
|IBM Lotus Domino|Last occurrence only|color=blue|
|IBM HTTP Server|First occurrence only|color=red|
|Node.js / express|First occurrence only|color=red|
|mod_perl, libapreq2 / Apache|First occurrence only|color=red|
|Perl CGI / Apache|First occurrence only|color=red|
|mod_wsgi (Python) / Apache|First occurrence only|color=red|
|Python / Zope|All occurrences in List data type|color=[‘red’,’blue’]|
## Impact
- **Bypassing security:** An attacker might provide a legitimate username and a malicious one. If the application checks for the first, it passes validation, but if the second is used to perform an action (like sending a reset token), it can be sent to the attacker's email address.
- **Altering application behavior:** By injecting different parameters, an attacker can override hardcoded values or bypass security checkpoints like input validation or access control.
- **Cross-site scripting (XSS):** By crafting a malicious parameter and injecting it into an existing one, an attacker could potentially trigger a cross-site scripting vulnerability.
- **Denial of Service:** In some cases, HPP can be used to flood an inbox with unwanted emails, effectively causing a denial of service.
# Common defenses against clickjacking attacks
- **Properly sanitize and encode input:** The most crucial step is to sanitize all user input to prevent attackers from injecting query string delimiters or other special characters.
- **Strictly enforce URL encoding:** Ensure all user input is properly encoded before being used in a URL or another parameter.
- **Use strict regular expressions:** When rewriting URLs or handling parameters, use strict regular expressions to validate and reject any malformed requests.
- **Configure Web Application Firewalls (WAFs):** Configure WAFs to identify and block potentially malicious requests that use duplicate parameters.
- **Centralize parameter handling:** Handle all parameters in a single, consistent location in your application's backend to avoid inconsistencies.