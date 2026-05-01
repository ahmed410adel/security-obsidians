![[information-disclosure.jpg]]
# What is information disclosure?
Information disclosure, also known as information leakage, is when a website unintentionally reveals sensitive information to its users. Depending on the context, websites may leak all kinds of information to a potential attacker, including:
- Data about other users, such as usernames or financial information
- Sensitive commercial or business data
- Technical details about the website and its infrastructure
## Examples of information disclosure
- Revealing the names of hidden directories, their structure, and their contents via a `robots.txt` file or directory listing
- Providing access to source code files via temporary backups
- Explicitly mentioning database table or column names in error messages
- Unnecessarily exposing highly sensitive information, such as credit card details
- Hard-coding API keys, IP addresses, database credentials, and so on in the source code
- Hinting at the existence or absence of resources, usernames, and so on via subtle differences in application behaviour
## How do information disclosure vulnerabilities arise?
- **Failure to remove internal content from public content**. For example, developer comments in markup are sometimes visible to users in the production environment.
- **Insecure configuration of the website and related technologies**. For example, failing to disable debugging and diagnostic features can sometimes provide attackers with useful tools to help them obtain sensitive information. Default configurations can also leave websites vulnerable, for example, by displaying overly verbose error messages.
- **Flawed design and behaviour of the application**. For example, if a website returns distinct responses when different error states occur, this can also allow attackers to enumerate sensitive data, such as valid user credentials.

#### Examples of high-level techniques and tools that you can use to help identify information disclosure vulnerabilities during testing.
##### 1. Fuzzing
If you identify interesting parameters, you can try submitting unexpected data types and specially crafted fuzz strings to see what effect this has. Pay close attention; although responses sometimes explicitly disclose interesting information, they can also hint at the application's behaviour more subtly. For example, this could be a slight difference in the time taken to process the request. Even if the content of an error message doesn't disclose anything, sometimes the fact that one error case was encountered instead of another one is useful information in itself.
##### 2. Scanner
provides live scanning features for auditing items while you browse, or you can schedule automated scans to crawl and audit the target site on your behalf. Both approaches will automatically flag many information disclosure vulnerabilities for you
##### 3. Using engagement tools
###### Search
You can use this tool to look for any expression within the selected item. You can fine-tune the results using various advanced search options, such as regex search or negative search. This is useful for quickly finding occurrences (or absences) of specific keywords of interest.
###### Find comments
You can use this tool to quickly extract any developer comments found in the selected item. It also provides tabs to instantly access the HTTP request/response cycle in which each comment was found.
###### Discover content
You can use this tool to identify additional content and functionality that is not linked from the website's visible content. This can be useful for finding additional directories and files that won't necessarily appear in the site map automatically.
## Common defences of information disclosure
#### Files for web crawlers
Many websites provide files at `/robots.txt` and `/sitemap.xml` to help crawlers navigate their site. Among other things, these files often list specific directories that the crawlers should skip, for example, because they may contain sensitive information.
#### Directory listings
#### Developer comments
#### Error messages
---
### what is TRACE method ?
TRACE and TRACK are **HTTP methods that are used to debug web server connections**. A local or remote unprivileged user may be able to abuse the HTTP TRACE/TRACK functionality to gain access to sensitive information in HTTP headers when making HTTP requests, This behaviour is often harmless, but occasionally leads to information disclosure, such as the name of internal authentication headers that may be appended to requests by reverse proxies.


## Labs
* #Portswigger
	1. **Information disclosure in error messages** #APPRENTICE 
		to disclose this lab information you have to send request with error on it like changing the product id to string rather than number then this error will disclose information
	2. **Information disclosure on debug page** #APPRENTICE 
		you will found a path in the page source use it and find the secret key
	3. **Source code disclosure via backup files** #APPRENTICE 
		find the directory in the `robots.txt` file then access the directory and find the database password
	4. **Authentication bypass via information disclosure** #APPRENTICE 
		use <mark style="background: #FF5582A6;">trace</mark> method to know the header used from the back-end and then use the header to access the admin 
	5. **Information disclosure in version control history** #PRACTITIONER 
		the admin password is leaked in the version control software which is `/.git` file so we will download this file and undo last operation to get the admin password
# Common defences against information disclosure 
- Make sure that everyone involved in producing the website is fully aware of what information is considered sensitive. Sometimes seemingly harmless information can be much more useful to an attacker than people realize. Highlighting these dangers can help make sure that sensitive information is handled more securely in general by your organization.
- Audit any code for potential information disclosure as part of your QA or build processes.
- Use generic error messages as much as possible. Don't provide attackers with clues about application behaviour unnecessarily.
- Double-check that any debugging or diagnostic features are disabled in the production environment.
- Make sure you fully understand the configuration settings, and security implications, of any third-party technology that you implement. Take the time to investigate and disable any features and settings that you don't actually need.
# NOTES
- you should avoid focussing too narrowly on a particular vulnerability. Sensitive data can be leaked in all kinds of places, so it is important not to miss anything that could be useful later
