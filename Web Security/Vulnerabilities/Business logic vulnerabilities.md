## Labs
* #Portswigger
	1. **Excessive trust in client-side controls** #APPRENTICE 
		change the price of the product while adding to the cart from the request
	2. **High-level logic vulnerability** #APPRENTICE 
		add the product to the cart and another product with a negative quantity to low the price
	3. **Inconsistent security controls** #APPRENTICE 
		to solve this lab you have to access the admin panel and delete Carlos account and to identify yourself as admin you have to set your email as `@dontwannacry.com` so you will register with your email and the update it
	4. **Flawed enforcement of business rules** #APPRENTICE 
		to buy the product to have to add the coupon but notice the you have another coupon so you will add them multiple times but do not add the same coupon twice in a row
	5. **Low-level logic flaw** #PRACTITIONER 
		to to buy the product you have to add a lot of it to make the code corrupt and the integer values end `int (2,147,483,647)` and the code start from `(-2,147,483,648)`so you will send 323 request from the product and then send to add 47 quantity again and then add the cheap products to make it able to buy 
	6. **Inconsistent handling of exceptional input** #PRACTITIONER 
		because of the user put the email in string which is 255 byte so you will write an email that exceed that limit `very-long-string@dontwannacry.com.YOUR-EMAIL-ID.web-security-academy.net` to make the email end with `@dontwannacry.com` to accept your account as administrator and the 2FA will sent to your email
	7. **Weak isolation on dual-use endpoint** #PRACTITIONER 
		delete the current-password parameter to solve the lab
	8. **Insufficient workflow validation** #PRACTITIONER 
		to buy the product you need buy a cheap product and intercept the last request and then put the needed product on the cart and then send the last request *the problem is the site do not validate if you pass through all requests sequentially*
	9. **Authentication bypass via flawed state machine** #PRACTITIONER 
		to solve the lab you have to drop `/role-selector` request then you will have the administrator role by default
	10. **Infinite money logic flaw** #PRACTITIONER 
		we notice that if we use `SIGNUP30` code we will get a sale so we use it to buy a gift coupon and then create macro with this and increase our money
	11. **Authentication bypass via encryption oracle** #PRACTITIONER 
	12. **Bypassing access controls using email address parsing discrepancies** #EXPERT 

# Common defences against business logic vulnerabilities
- Maintain clear design documents and data flows for all transactions and workflows
- Write code as clearly as possible
- Note any references to other code that uses each component. Think about any side-effects of these dependencies if a malicious party were to manipulate them in an unusual way.
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
    score: 100
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