---
weight: 6
title: "Web Authentication"
# bookFlatSection: true
# bookToc: false
bookCollapseSection: false
---
# Authentication in web services
How does an application figure out who is talking to it? What part of the identity of that person / system is enough?

e-mails & passwords are one way. WebAuthn is another.
kerberos is a good option inside an enterprise. it provides a convenient SSO experience when combined with SPNEGO.
session tokens passed on as cookies can also work depending on the use case.

I'm gonna look at some relatively modern ones.
Note that unlike the above, these aren't cryptographic protocols. In the end they still use one of the above ways to authenticate the user at some point.

## OIDC
OAuth 2.0 is the main thing. OIDC is just a standard way to do authentication with OAuth. Else ppl were making their own implementaitons.  
OAuth 2.0 itself is an authorization protocol to provide one app (e.g., App A) access to a user's resources in another app (e.g., App B), after authenticating the user & getting their consent via an authorization server.
In the case of OIDC, the App A also gets an ID card of the user along with the access token of App B.  
That ID card is sent as a JWT (JSON Web Token), which is a format.
The authorization server could be App B itself, or another service that it trusts.

The flow is like this (one of many OAuth 2.0 "flows". this one is the web server flow):
* User wants to login to App A
* They click on a "login with App B" button on the App A
* App A redirects user to the authorization server for App B (the resource server)
* The authorization server checks whether the user has logged into App B, or if not, asks them to log in
* The authorization server checks whether the user provides their consent
* User is redirected to App A, which now has an authorization code
* App A asks for an access token & ID token from authz server. this happens in App A's backend server.
the access token will be used to talk to App B and it is supposed to be a SECRET!
ID token is a JWT which is like an ID Card of the user. JWT is just encoded and not encrypted. it is signed though.
* App A now knows who the user is, as per the users records in App B's system
* App A can talk to App B via it's backend server, on behalf of the user, as per the scope of the access token

Tech jargon:
* client id & client secret which App A (the client) uses to talk to the authorization server. doesn't involve the user
* callback URL / redirect URI, provided by App A. it is the URI that authorization server should redirect user to, if consent granted
* response type. what kinda response the client wants to get after authz server gets the user's consent
* authorization code. the default response type. this is a not-so-secret string that the App A will use for comms
with the authorization server when talking about that particular user. it's like a session cookie.
the authz server will only accept if it is accompanied with a client secret.
* JWT. the ID token of a particular user that authorization server sends to App A
as a response when it sends the request along with the authorization code.
* registeration. when the client sets up an account with the authz server. it needs to give a redirect_uri & some details. gets a client id, secret.

if App A runs entirely on the client device, e.g., PWA / Mobile App, then they can't use the client secret, since it will be exposed to the user / public.
in such cases, there's a PKCE flow which can be used.  

there's also an Implicit flow for such devices, where instead of authz server sending an authorization code, it just sends the access token directly.
however, this is a bit risky, since a secret is now on the client's device. it should be stored safely, e.g., via a httpOnly cookie.

there's a client credentials flow which doesn't involve the user at all, and used for system to system communication only.

there's a device flow for cases where user isn't able to interact on a device to provide their consent. e.g., a terminal.  
in such cases, the device will ask user to open a link and type in a code to do the authorization grant with the authorization server.

OAuth 2.0 is a web standard. [IETF RFC](https://www.rfc-editor.org/info/rfc6749), whereas OAuth 1.0 was just a name used by some libraries floating around in the initial days before the standard got developed.

OIDC is not a web standard (maybe it needn't have been), but they have a standard written on their site : <https://openid.net>

## JWT
JSON Web Token? With such a generic name, it's sure sounds like trouble. But it's also a web standard :O [IETF RFC](https://datatracker.ietf.org/doc/html/rfc7519).

It consists of three parts. Once the JSONs of all three are encoded, it becomes a URL safe string, with a '.' separating the three parts. the parts are:
* header. telling the subtype of token, algo used for signing etc.
* body. whatever JSON. all custom data, including secrets and stuff go here. the stuff here is also known as "claim(s)"
* signature. this is used to verify that the token was generated by the authorization service and not tampered with

Once authenticated, the JWT is used to identify the client. Like a sessionID cookie. But unlike the cookie, the JWT has meaning when decoded & can be decoded
to get info about the user, e.g., username, email, permissions etc. Once the server sends the JWT to the client, the client can send it along with every request
as part of the Authorization header. Because of the signature, the server knows the token isn't tampered with. It is also possible for the server to reject some
JWTs after an expiration period, or expire them if they suspect a leak.

Some websites also use a concept of _refresh token_ which allows the access token to be refreshed frequently to reduce impacts of leaks.
A refresh token is has a longer expiry and can be used to refresh access tokens. What if a refresh token leaks? Yeah, it could.
It should be stored in a safer place. And like access tokens, they can be invalidated.

JWTs became popular since they contain info about the user,
that info can be used by the server for app logic instead of going to the DB to get info about the user who made the request.
That also means that a JWT from one server can be sent to another related server and that needn't re-authenticate the user. Not really possible with sessionIds.
However, this increases the request size if there's too much info being stored in the token.

Since it identifies a user, they should make sure to keep it secret. Should be treated at the same secrecy level as a password.

## SAML
Is older than OAuth. Current version is SAML 2.0, but not actively being developed. Meant for SSO. It provides authentication and authorization.  
Frequently used in internal enterprise systems. Uses a central IdP (identity provider) which all services talk to.
Why would you ever use this? It's already used in multiple places. And the flow seems simpler than OAuth. However, OAuth is the preferred choice for new apps.

## FAQs
These are questions that came up while I was exploring different posts / videos.

1. Why is a refresh token required if the access token itself could be used to get new ones?
Uhh.. IDK. The claim is that a refresh token can be invalidated, but then so could access tokens.

2. Why provide a "state" param in the auth_code request?
If you want to give the redirect_uri of your site some data about the state of the app from which the user was sent to the authz server.
The other case is to prevent CSRF. Your site should be the able to determine if the state string indeed came from it.
Else, someone could make a auth URL which will then ask the user to login, consent & send them to your site.

more details: <https://stackoverflow.com/q/26132066/13229013>

3. What's the diff b/w state & nonce?
state is for browser based flows whereas nonce is for the id token request.
to verify that the server is indeed returing a response to your request & prevents replay attacks.
state could have some meaning encoded whereas nonce is a random number.

still don't get it fully. nonce can inform a user of MiTM attack, but it can't really prevent it I guess.

more details: <https://stackoverflow.com/q/46844285/13229013>

4. Why does redirect_uri need to be part of the access token request?
This one is actually a topic of discussion. Some claim that it's not really useful.
Apparently, not registerting a static redirect_uri can lead to phishing attacks if someone creates a link to make authz server send users to a rogue site.
but that's only applicable for auth code requests. what about the access token request? which doesn't redirect.

all this shouldn't happen when using the client secret.

more details: <https://stackoverflow.com/questions/37659188/why-is-redirect-uri-required-on-access-token-request>