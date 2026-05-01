# 1. SQL identification
first thing to do after knowing that there is a SQLi is to get the database type
`select * from sqlite_master`

# 2. LFI to LFD scenario
use PHP wrapper
`php://filter/convert.base64-encode/resource=index`
# 3. Always true compare
if the token failed to validate because it is false make its value equal true

# 4. Bypass PHP code validation
`echo backtic ls backtic`
# Nginx path traversal
`http://domain/root../`