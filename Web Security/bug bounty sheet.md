## File Upload
- .phtml, .php3, .php4, .php5, .php7, .pht, .phar
- Double extensions
- Case manipulation
- shell.php%00.jpg, shell.php\x00.jpg
- shell.php.
	shell.php....
	shell.php%20
	shell.php::$DATA   (NTFS ADS trick on Windows/IIS)
- Content-Type / magic byte spoofing
- .htaccess       → redefine which extensions PHP handles (AddType application/x-httpd-php .jpg)
	.user.ini       → auto_prepend_file trick for PHP-FPM setups
	web.config      → IIS equivalent
## CORS
- `https://target.com.adel.com`
- `https://adel.com/target.com`
- `https://targetcom.adel.com`
- `https://adeltarget.com`
- `https://example.com.{.attacker.com/`
- `https://example.com.}.attacker.com/`
- `https://example.com.'.attacker.com/`
- `https://target.application}.arbitrary.com`
- `https://target.application_.arbitrary.com`
- `https://attacker.com?target.com`
- `https://attacker.com#target.com`
- array
- double origins

## Recon
### Subdomains
- subfinder -d target.com
- amass enum -passive -d target.com
- dnsx -resp-only -l subdomains.txt
- httpx -tech-detect -title -status-code
---
- subfinder -d target.com -all -silent
- assetfinder --subs-only target.com
- amass enum -active -d target.com
- gau target.com
- waybackurls target.com
- hakrawler -url https://target.com
- katana -u https://target.com -jc
`cat urls.txt | grep -Ei "debug|internal|test|staging|preview|cache|proxy|graphql|edge|cdn"`



#### Headers
- `Forwarded: host=evil.com`
- `X-Forwarded-Scheme: nothttps`
- `X-Forwarded-Host`  
- `X-Forwarded-Proto  `
- `X-Original-URL`
- `X-Rewrite-URL`