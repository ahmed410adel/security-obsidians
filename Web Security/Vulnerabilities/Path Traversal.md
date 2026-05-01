![[directory-traversal.svg]]
# What is path traversal?
Path traversal is also known as directory traversal. These vulnerabilities enable an attacker to read arbitrary files on the server that is running an application. This might include:
- Application code and data.
- Credentials for back-end systems.
- Sensitive operating system files.
On Windows, both `../` and `..\` are valid directory traversal sequences. The following is an example of an equivalent attack against a Windows-based server:
`https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini`

## LFI Local File Inclusion
The File Inclusion vulnerability allows an attacker to include a file, usually exploiting a "dynamic file inclusion" mechanisms implemented in the target application. The vulnerability occurs due to the use of user-supplied input without proper validation in LFI the code only executed not displayed
#### Functions that could result in local file inclusion vulnerability in PHP:  
* `Include()`  
* `Include_once()`  
* `Require()` 
* `Require_once()`

## LFD Local File Disclosure
In most cases it is considered the same as Local file inclusion, except that LFD can only  
disclose the file contents, and can NOT be used for file inclusion or execution
in LFD the code only displayed not executed
*add `?` to make the code execute*

## RFI Remote File Inclusion
Remote File Inclusion (also known as RFI) is the process of including remote files from your server
this vulnerability is now out dated because the PHP do not support to include files from out server

## LFI => LFD
* using PHP wrapper `file:///etc/passwd` and `expect://command` (expect is not enable by default)
* using (PHP wrapper use filter) `vulnerable.php?file=php://filter/read=convert.base64-encode/resourse=../../config.php`
	we used `convert.base64-encode` to encode the source code and display it as base64 without executing but if we read the file without encoding it , it will be executed and we will not read the source code
## LFI => RCE
* using file upload pages to upload your code and then include it so the code will be executed
* using PHP session 
	in PHP the sessions is stored in `/var/lib/php5/sess_[PHPSESSIONID]` so if I stored PHP code rather than real session id and then include this file I will get RCE
* using PHP `expect://command` wrapper
* `POST /any/1.php?page=php://input    <?php phpinfo();?>` (telling the PHP that you are getting input)
* using log file by manipulate my user-agent and add PHP code on it and then read the log file waiting the code to be executed (I have to be able to read Apache logs)
* same as log file you can inject the user-agent `/proc/self/environ` (file that display environment variables)
* create file that point to another file that I want to read (symbolic shortcuts)
* `tmp_name` file

## Labs
* #Portswigger
	1. **File path traversal, simple case** #APPRENTICE 
		path traversal vulnerability in the display of product images
	2. **File path traversal, traversal sequences blocked with absolute path bypass** #PRACTITIONER 
		Modify the `filename` parameter, giving it the value `/etc/passwd`
	3. **File path traversal, traversal sequences stripped non-recursively** #PRACTITIONER 
		it strips the `../` from the file name so the payload will be ``....//....//....//etc/passwd`
	4. **File path traversal, traversal sequences stripped with superfluous URL-decode** #PRACTITIONER 
		double URL encode you payload
	5.  **File path traversal, validation of start of path** #PRACTITIONER 
		it validates on the first directory that their file start with so the payload will be `/var/www/images/../../../etc/passwd`
	6. **File path traversal, validation of file extension with null byte bypass** #PRACTITIONER 
		this lab validates the extension of the file so will terminate our payload using null terminator so the payload will be `../../../etc/passwd%00.png`

# Common defences against path traversal
- Validate the user input before processing it and compare the user input with a whitelist of permitted values.
- verify that the input contains only permitted content, such as alphanumeric characters only.
- append the input to the base directory and use a platform filesystem API to canonicalize the path. Verify that the canonicalized path starts with the expected base directory
	1. creating new file that combines *base directory* with *user input*
	2. canonicalize the path to resolve any symbolic links (`../ or ./`)
	3. Verify the canonical path starts with the intended `BASE_DIRECTORY`. If not, reject the request
	**CODE** :
	`File file = new File(BASE_DIRECTORY, userInput);` 
	`if (file.getCanonicalPath().startsWith(BASE_DIRECTORY))` 
	`{ // process file }`
- Disallow external URLs in file-handling parameters
- Use libraries that handle file path sanitization
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
    score: 40

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


---
## NOTES
* test LFI in any user input that accept files
## Convert Attacks
#### 1. LFI => LFD
1. using PHP wrapper `file:///etc/passwd` and `expect://command` (expect is not enable by default)
2. using (PHP wrapper use filter) `vulnerable.php?file=php://filter/convert.base64-encode/resourse=../../config.php`
	we used `convert.base64-encode` to encode the source code and display it as base64 without executing but if we read the file without encoding it , it will be executed and we will not read the source code
#### 2. LFI => RCE
1. using file upload pages to upload your code and then include it so the code will be executed
2. using PHP session 
	in PHP the sessions is stored in `/var/lib/php5/sess_[PHPSESSIONID]` so if I stored PHP code rather than real session id and then include this file I will get RCE
3. using PHP `expect://command` wrapper
4. `POST /any/1.php?page=php://input    <?php phpinfo();?>` (telling the PHP that you are getting input)
5. using log file by manipulate my user-agent and add PHP code on it and then read the log file waiting the code to be executed (I have to be able to read Apache logs)
6. same as log file you can inject the user-agent `/proc/self/environ` (file that display environment variables)
7. create file that point to another file that I want to read (symbolic shortcuts)
8. `tmp_name` file