## Information Gathering
1. i have collected the in scope subdomains 
2. read the whole policy
3. use tools
	1. `subfinder -dL in-scope-subdomains -all -silent > subfinder`
	![[Pasted image 20261017095639.png|482]]
	2. `curl -s "https://crt.sh/?q=%25.example.com&output=json" | jq -r '.[].name_value' >> subs.txt`
	3. `whoxy history`
		`domain-admin@twilio.com`, `domains@twilio.com` -> admin email
		`18778894546`, `+1.877889455` -> phone number
	4. `whois reverse` use the previous information and reverse on it to get some domains and subdomains

## Scanning
#### CSRF
	test change email owner

#### GraphQL