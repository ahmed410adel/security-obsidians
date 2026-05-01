# What are JWTs?
JSON web tokens (JWTs) are a standardized format for sending cryptographically signed JSON data between systems. They can theoretically contain any kind of data, but are most commonly used to send information ("claims") about users as part of authentication, session handling, and access control mechanisms
> As JWTs are most commonly used in *authentication*, *session management*, and *access control mechanisms*, these vulnerabilities can potentially compromise the entire website and its users.

![[jwt-infographic.jpg]]

## what is the problem that JWTs designed to solve ?
the problem is that the old session token need to be stored on the server and this makes the website need a larger storage to save all of these sessions unlike the JWT it does not saved on the server it is only saved at the user browser.
### Process
1. the user logged in with user and password
2. the server generate JWT with private key and do not save it
3. the server send JWT to the user browser
4. the user send this JWT to the server every request
5. the server validates the user identity by concatenating the header and payload and hashing them using private key if the output equal the signature then the user will be logged in 

## JWT format
A JWT consists of 3 parts: a **header**, a **payload**, and a **signature**. These are each separated by a dot, as shown in the following example:
`eyJraWQiOiI5MTM2ZGRiMy1jYjBhLTRhMTktYTA3ZS1lYWRmNWE0NGM4YjUiLCJhbGciOiJSUzI1NiJ9.eyJpc3MiOiJwb3J0c3dpZ2dlciIsImV4cCI6MTY0ODAzNzE2NCwibmFtZSI6IkNhcmxvcyBNb250b3lhIiwic3ViIjoiY2FybG9zIiwicm9sZSI6ImJsb2dfYXV0aG9yIiwiZW1haWwiOiJjYXJsb3NAY2FybG9zLW1vbnRveWEubmV0IiwiaWF0IjoxNTE2MjM5MDIyfQ.SYZBPIBg2CRjXAJ8vCER0LA_ENjII1JakvNQoP-Hw6GG1zfl4JyngsZReIfqRvIAEi5L4HV0q7_9qGhQZvy9ZdxEJbwTxRs_6Lb-fZTDpW6lKYNdMyjw45_alSCZ1fypsMWz_2mTpQzil0lOtps5Ei_z7mM7M8gCwe_AGpI53JxduQOaB5HkT5gVrv9cKu9CsW5MS6ZbqYXpGyOG5ehoxqm8DL5tFYaW3lB50ELxi0KsuTKEbD0t5BCl0aCR2MBJWAbN-xeLwEenaqBiwPVvKixYleeDQiBEIylFdNNIMviKRgXiYuAvMziVPbwSgkZVHeEdF5MQP1Oe2Spac-6IfA`

The header and payload parts of a JWT are just base64url-encoded JSON objects. The header contains metadata about the token itself, while the payload contains the actual "claims" about the user. For example, you can decode the payload from the token above to reveal the following claims:
`{`
`    "iss": "portswigger",`
`    "exp": 1648037164,`
`    "name": "Carlos Montoya",`
`    "sub": "carlos",`
`    "role": "blog_author",`
`    "email": "carlos@carlos-montoya.net",`
`    "iat": 1516239022`
`}`

In most cases, this data can be easily read or modified by anyone with access to the token. Therefore, the security of any JWT-based mechanism is heavily reliant on the cryptographic signature.
## JWT vs JWS vs JWE
The JWT specification is actually very limited. It only defines a format for representing information ("claims") as a JSON object that can be transferred between two parties. In practice, JWTs aren't really used as a standalone entity. The JWT spec is extended by both the JSON Web Signature (JWS) and JSON Web Encryption (JWE) specifications, which define concrete ways of actually implementing JWTs
![[jwt-jws-jwe.jpg]]

# What are JWT attacks?
JWT attacks involve a user sending modified JWTs to the server in order to achieve a malicious goal. Typically, this goal is to bypass authentication and access controls by impersonating another user who has already been authenticated
# What is the impact of JWT attacks?
The impact of JWT attacks is usually severe. If an attacker is able to create their own valid tokens with arbitrary values, they may be able to escalate their own privileges or impersonate other users, taking full control of their accounts
# JWT header parameter injections
According to the JWS specification, only the `alg` header parameter is mandatory. In practice, however, JWT headers (also known as JOSE headers) often contain several other parameters. The following ones are of particular interest to attackers.
- `jwk` (JSON Web Key) - Provides an embedded JSON object representing the key.
- `jku` (JSON Web Key Set URL) - Provides a URL from which servers can fetch a set of keys containing the correct key.
- `kid` (Key ID) - Provides an ID that servers can use to identify the correct key in cases where there are multiple keys to choose from. Depending on the format of the key, this may have a matching `kid` parameter.
---
#### Quick Reminder: RS256 = RSA + SHA256

    RS256 is a combination of:

        SHA-256: to hash the data

        RSA: to sign and verify
#### Core Concept of RSA
RSA uses a key pair:

    🔐 Private key = used to sign

    📢 Public key = used to verify

These keys are mathematically connected:
    What the private key encrypts, the public key decrypts

    What the public key encrypts, the private key decrypts

But in signing, we flip the usual idea of encryption. Here's how it works:
1. Create the data to sign
`base64url(header) + "." + base64url(payload)`
*Example:*
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjoiYWRtaW4ifQ

2. Hash the data using SHA-256
Before signing, we hash the above string:
`SHA256(data) → digest (fixed 256-bit value)`
*Let’s say it gives this digest:*
3b7f...d8a1

3. Encrypt the hash using the PRIVATE key
`signature = RSA_encrypt(private_key, hash)`
*That encrypted hash is added as the third part of the JWT:*
`header.payload.signature`

4. 🔍 How Is the Signature Verified (Using the PUBLIC key)?

Here comes the magic of asymmetric crypto:
The receiver:

    Gets:

        The header and payload

        The signature

        The public key (from a trusted JWKS or local config)

    Recomputes the hash from header + payload:

`hash = SHA256(header.payload)`
- Decrypts the signature using the public key:
`decrypted_signature = RSA_decrypt(public_key, signature)`

Compares:
if `decrypted_signature` == `hash`:
    ✅ Signature is valid
else:
    ❌ Invalid or tampered

---
Other algorithms, such as RS256 (RSA + SHA-256) use an "asymmetric" key pair. This consists of a private key, which the server uses to sign the token, and a mathematically related public key that can be used to verify the signature.
![[jwt-asymmetric-signing-algorithm.jpg]]
 **Signing with the Private Key**: When the server wants to create a JWT, it uses its private key to generate a digital signature for the token. The signing process ensures that the token hasn't been tampered with after it was issued.
 **Verifying with the Public Key**: The recipient (or any third party) can use the public key to verify the authenticity of the token. If the token's signature can be verified with the public key, it means that it was indeed signed by the server holding the corresponding private key. This confirms both the integrity and authenticity of the JWT.

## Algorithm confusion attacks
Algorithm confusion attacks (also known as key confusion attacks) occur when an attacker is able to force the server to verify the signature of a JSON web token JWT using a different algorithm than is intended by the website's developers. If this case isn't handled properly, this may enable attackers to forge valid JWTs containing arbitrary values without needing to know the server's secret signing key.
##### The Problem
JWTs support **two main types of algorithms**:
1. **Symmetric algorithms (e.g., HS256):**
    - Use the same secret key for signing and verification.
    - The key must remain confidential.
2. **Asymmetric algorithms (e.g., RS256):**
    - Use a **private key** for signing (kept secret).
    - Use a **public key** for verification (shared with everyone).
**Confusion arises** because:
- Some servers trust the `alg` field in the JWT header blindly to decide which algorithm to use for verification.
- If an attacker can manipulate the `alg` field, they can force the server to verify the signature using a weaker or unintended algorithm.
## Other interesting JWT header parameters
The following header parameters may also be interesting for attackers:
- `cty` (Content Type) - Sometimes used to declare a media type for the content in the JWT payload. This is usually omitted from the header, but the underlying parsing library may support it anyway. If you have found a way to bypass signature verification, you can try injecting a `cty` header to change the content type to `text/xml` or `application/x-java-serialized-object`, which can potentially enable new vectors for XXE and deserialization attacks.
- `x5c` (X.509 Certificate Chain) - Sometimes used to pass the X.509 public key certificate or certificate chain of the key used to digitally sign the JWT. This header parameter can be used to inject self-signed certificates, similar to the `jwk` header injection attacks discussed above. Due to the complexity of the X.509 format and its extensions, parsing these certificates can also introduce vulnerabilities.

## Labs
* #Portswigger
	1. **JWT authentication bypass via unverified signature** #APPRENTICE 
		I changed the `sub` value to *administrator* and generate a new token then I accessed the admin page and change the path to delete Carlos account because this lab do not verify signature
	2. **JWT authentication bypass via flawed signature verification** #APPRENTICE 
		change the `alg` to *none* to make the server do not expect signature and delete the signature from the cookie but let the dot and change the `sub` to *administrator*
	3. **JWT authentication bypass via weak signing key**  #PRACTITIONER 
		in this lab we will try to get the encryption key that the JWT has been encrypted with by brute-force know and default keys and because the encryption is symmetric we will get the key to make signatures
		*part 1*
		`hashcat -a 0 -m 16500 <JWT> <wordlist>` to fuzz the key => (secret1)
		*part 2*
		use the key in JWT editor to make the administrator signature
	4. **JWT authentication bypass via JWK header injection** #PRACTITIONER 
		I generated my RSA256 key and embed it on JWK header to make the server use it as the validate on the signature, the server must validate on the key with whitelisted keys 
	5. **JWT authentication bypass via JKU header injection** #PRACTITIONER 
		the solution here is to add the URL that the server will choose the public key from and this URL will be our server link to a key that we have been generated the problem here is that the server trust any URL and sign your header and payload with the same RSA key
		*code on our server :*
		`{`
	    `"keys": [`
			`{`
		    `"kty": "RSA",`
		    `"e": "AQAB",`
		    `"kid": "ADEL",`
		    `"n": "1p1uDKw_ARXClpS7AyqK5YBYYmMhGANob4wVUOuXUgwl9S37wNUheI6tvjYHTsRGHGaoe_oc7cbFapZZe9YzgRbB03wjkLYN3ujiBI2BHJ_eNst3ka21xzU79gto_NeUEtYIXvmUZqa8sZaHbhb7jyXkUuhrDd0i4YnWrOj_BvzM2GoQHQOcPTcIfwg1SzqEWKAMrWRSZj1d4h4pMf20M7p2xY3bHr-9ocEQRoBaTFxdfM5wiJ9U_digsqBp7qZnYpSl8xOjgVxWX7ZsgBDy-6avVNWPdMw3wS5ryjoiTTuLHAAqIajl18WzkFXqTsf0Ie-aTBeyk8fWncZtIiyCQw"`
			`}`
	    `]`
		`}`
	6. **JWT authentication bypass via kid header path traversal** #PRACTITIONER 
		**`/dev/null`** is a special file on Unix/Linux systems. It acts like a "black hole" that discards all data written to it and always returns nothing when read and You generate a new symmetric key with a null byte (`AA==`) as the key content The null byte key (`AA==`) is used as a "fake" signing key to sign the modified JWT By exploiting a path traversal vulnerability in the `kid` parameter, you trick the server into accepting an empty key from `/dev/null`. This allows you to sign a JWT pretending to be the administrator, thereby bypassing authentication and accessing the admin panel
	7. **JWT authentication bypass via algorithm confusion** #EXPERT 
		applying algorithm confusion attack :
		copy the public key from `jwks.json` copy the PEM format of the RSA key by creating new RSA key and put the key on the extension input and copy its PEM then base64 encode the PEM key then create symmetric key from it using the encoded value as a key and finally change the token payload and sign it
	8. **JWT authentication bypass via algorithm confusion with no exposed key** #EXPERT 
		login twice to copy two JWTs and then pass them to this code :
		`docker run --rm -it portswigger/sig2n <token1> <token2>` ***(Black Box)*** 
		then try every tampered JWT with the website until we reach to the one that do not log us out from the website after that we will take the base64 key of this JWT and create symmetric key with it and finally change the token payload and sign it



# Common defences against JWT attacks
You can protect your own websites against many of the attacks we've covered by taking the following high-level measures:
- Use an up-to-date library for handling JWTs and make sure your developers fully understand how it works, along with any security implications. Modern libraries make it more difficult for you to inadvertently implement them insecurely, but this isn't fool proof due to the inherent flexibility of the related specifications.
- Make sure that you perform robust signature verification on any JWTs that you receive, and account for edge-cases such as JWTs signed using unexpected algorithms.
- Enforce a strict whitelist of permitted hosts for the `jku` header.
- Make sure that you're not vulnerable to path traversal or SQL injection via the `kid` header parameter.
- Always set an expiration date for any tokens that you issue.
- Avoid sending tokens in URL parameters where possible.
- Include the `aud` (audience) claim (or similar) to specify the intended recipient of the token. This prevents it from being used on different websites.
- Enable the issuing server to revoke tokens (on logout, for example).
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
    score: 10
  - item: "Client-Side Code"
    user: "Effective against the attack"
    score: 5
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

# NOTES
- the security of any JWT-based mechanism is heavily reliant on the cryptographic signature
- Even if the token is unsigned, the payload part must still be terminated with a trailing dot
- A JWK (JSON Web Key) is a standardized format for representing keys as a JSON object.