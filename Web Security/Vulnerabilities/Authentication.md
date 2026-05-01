# What is authentication?
Authentication is the process of verifying the identity of a user or client.
##### There are three main types of authentication:
1. **Something you** know, such as a password or the answer to a security question. These are sometimes called "knowledge factors".
2. **Something you have**, This is a physical object such as a mobile phone or security token. These are sometimes called "possession factors".
3. **Something you are or do**. For example, your biometrics or patterns of behavior. These are sometimes called "inherence factors".
![[password-reset-poisoning.svg]]
# authentication and authorization
Authentication is the process of verifying that a user is who they claim to be. Authorization involves verifying whether a user is allowed to do something.

# authentication mechanisms
- Vulnerabilities in password-based login
- Vulnerabilities in multi-factor authentication
- Vulnerabilities in other authentication mechanisms

# brute-force protection
- Locking the account that the remote user is trying to access
- Blocking the remote user's IP address if they make too many login attempts in quick succession




## Labs
* #Portswigger
	1. **Username enumeration via different responses** #APPRENTICE 
		the account page display error message for username and password which makes you know does the user is wrong the pass is wrong
	2. **2FA simple bypass** #APPRENTICE 
		even if you do not complete the 2FA it will login
	3. **Password reset broken logic** #APPRENTICE 
		the token that verify the user who is changing his password does not being validated
	4. **Username enumeration via subtly different responses** #PRACTITIONER 
		it gives you different response when the username is true
	5. **Username enumeration via response timing** #PRACTITIONER 
		this lab limit your trail so we will use `X-Forwarded-For` header to spoof our IP then we will send our usernames list and write a big password because we notice that the response time is increasing with the password length increasing (that if the username is true)
		but if it is wrong the time response will be normal  
	6. **Broken brute-force protection, IP block** #PRACTITIONER 
		this lab try to block brute-force even `X-Forwarded-For` do not work but the the problem is that if you login 3 times with wrong data it will blocks you but if you do it twice and then login in with right credential it will reset again
	7. **Username enumeration via account lock** #PRACTITIONER 
		in this lab when you try the username many times with wrong password it display a message say 'you try many times' so you understand that this is the right username and then you can brute-force the password
	8. **2FA broken logic** #PRACTITIONER 
		you have the attacker credentials and the victim credentials so you will login as attacker and change the cookie parameter called "verify" to the victim username so the site will send the OTP to the victim and will wait you to write it and you do not have the OTP so you will brute-force it
	9. **Brute-forcing a stay-logged-in cookie** #PRACTITIONER 
		to solve this lab you must crack the `stay-logged-in` cookie header and brute-force it using burp suit payload processing
	10. **Offline password cracking** #PRACTITIONER 
		XSS lead to stealing `stay-logged-in` cookies and crack it then get the password
	11. **Password reset poisoning via middleware** #PRACTITIONER 
		create a host header attack using `X-Forwarded-Host` header to redirect Carlos email to your email and steal the token
	12. **Password brute-force via password change** #PRACTITIONER 
		to solve this lab when you login as Wiener you notice that there are change password function when you try to brute-force the password it loges you out but if you try to brute force the password and write different password as new password and confirm new password it will write you a new message as indicator
		QUESTION : 
		why do not we just brute-force password and write the same passwords while confirmation ?
		the answer is we do not want the request to go to the server because if the request goes to the server it will log you out because of you wrong password but if we write the different passwords in confirmation it will block the request from going to the server from the client side and we will not get a one minute time block from the server
	13. **Broken brute-force protection, multiple credentials per request** #EXPERT 
		The lab was likely designed to demonstrate the vulnerability of a weak or unprotected authentication mechanism to brute-force attacks. By submitting multiple passwords in a single request by submitting all password in array in JSON format
	14. **2FA bypass using a brute-force attack** #EXPERT 
		in this lab the you have the credential but you have to bypass the 2FA which loges you out after two wrong answer so you will create a macro to automat this operation and brute-force the the OTP
		This raises the question of how the macro and brute-force attack could still work in this context ? How It Works Despite New Codes Being Sent ?
		because the web applications might issue the same 2FA code for a single session, even if you log out and log back in. The code might only change after a certain period or under specific conditions, rather than every time you log in

# Common defenses against authentication vulnerabilities
1. enforce redirecting any attempted HTTP requests to HTTPS
2. Don't count on users for security
3. Prevent username enumeration
4. brute-force protection
	- IP-based user rate limiting
	- require the user to complete a CAPTCHA test
5. check your verification logic
6. Implement proper multi-factor authentication
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
    score: 30
  - item: "Server"
    user: "Effective against the attack"
    score: 70
  - item: "Client-Side Code"
    user: "Effective against the attack"
    score: 5
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

# Techniques
#### 1. Add null byte
try register with email and then log out and create new account with the same email but before sending the request add null byte after email value `%00` if the the email created you have done account take over