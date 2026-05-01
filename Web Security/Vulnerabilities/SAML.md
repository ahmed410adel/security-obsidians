Security Assertion Markup Language (SAML 2.0) is a complex authentication standard built on insecure and outdated XML technology

The idea behind single sign on is allow user to login to multiple websites using only on credentials or one account (easier login)

###### SAML Terms
- **Identity Provider:** Who provides the identity of the user for other websites to use (Google, Facebook, etc.)
- **Service Provider:** The app that asks for the identity of the user to login with it
- **SAML Request:** When the service provider sends a communication for identity provider
- **SAML Response:** When the identity provider response back to the service provider
- **Assertions:** Statement that asserted about the user
- **DSig:** XML signature that assign the assertions 
- **Assertion Consumer Service:** The endpoint that process the SAML response from the identity provider
- **Attribute:** user information
- **Relay State:** where the user was before authentication
- **SAML Trust:** configuration between service provider and identity provider
- **Metadata:** allow self configuration

#### We have 3 Protocols for single sign on (SSO)
- OAuth2
- SAML
- WS-FED

now after knowing what is the prepose of SSO let me tell you what is the difference between all of these technology. The SSO type define how the *service provider* and *identity provider* exchange information 
So the technology we talking about is SAML which use an **XML file** to exchange information

#### Service Provider-initiated SAML Flow
![[Pasted image 20260126221810.png]]
The Service Provider-Initiated (SP-Initiated) SAML flow is the most common way users authenticate through SAML. It starts when a user tries to access a protected resource on the service provider’s website. Since the user is not yet authenticated, the service provider generates a SAML authentication request and redirects the user to the Identity Provider (IdP) for verification.

The IdP receives this request, verifies its validity, and then issues a SAML Response containing a digitally signed Assertion that confirms the user’s identity. This response is sent back via the user’s browser to the service provider (SP). The SP then verifies the digital signature and extracts user information (such as username and email) from the Assertion. If the signature and data are valid, access is granted.