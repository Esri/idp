# OIDC resources and Frequently Asked Questions

## OIDC resources
* [RFC 6749 The OAuth 2.0 Authorization Framework](https://datatracker.ietf.org/doc/html/rfc6749)
* [OpenID Connect specifications](https://openid.net/developers/specs/)
* [Youtube video: Everything You Ever Wanted to Know About OAuth and OIDC](https://youtu.be/8aCyojTIW6U)
* [RFC 7636: Proof Key for Code Exchange by OAuth Public Clients](https://datatracker.ietf.org/doc/html/rfc7636)
* [Youtube video: Authorization code injection demo](https://www.youtube.com/live/moQidjdV5cw)

## Frequently Asked Questions

### Is ArcGIS a public or confidential client?

ArcGIS is a server-side, confidential client that uses the OpenID Connect authorization code flow for user authentication. ArcGIS obtains the ID and access tokens through a secure, backchannel request.

<!--
### 
OAuth 2.0 public clients are susceptible to authorization code interception attacks. PKCE was designed to secure public clients such as an SPA(Single Page Application) and clients running on a smartphone. In a single page application, the entire source code of the OAuth client is accessible in the user agent(web browser), compromising the client secret. In smartphones, the authorization code in the response from the authorization server can be intercepted by unauthorized applications. Also, since the application is installed on a user controlled device, is also possible to decompile the app and extract the client secret.

PKCE is recommended for use with confidential clients, since the user agent could also be compromised.
-->

