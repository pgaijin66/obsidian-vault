Type: #Note 
Tags: #0auth #oidc
# OAuth - OIDC
Created date: 2023-10-07 13:17

## Revision

### Digital identity

Unique representation of entity ( individual, org, device ) in digital world ( eg: username, email, pub keys, biometric data etc ). These are used to verify users identity in digital world.

## Encryption vs Hashing

**Encryption**: Converting plain text into cipher text using mathematical algorithm and a encryption key.( cookie . It is a two-way process

**Hashing**: Process of converting plain text into fixed length string of chars called hash. A good hash should be a one-way process, meaning once data is hashed it cannot be easily reversed to obtain original input.

Question:

1. Is Bcrypt hashing or encrypting ?
    
2. Is Base64 hashing or encrypting ?
    

## Authentication vs Authorization

**Authentication**: Verifying if user is who they claim to be

**Authorization**: Verifying if user has access to resources the resource they want to use.

## Token

Data that represents that user identity or used to provide access to specific resource.

**Types**:

1. **Access token:** Client uses this token to access the Resource Server (API). They are meant to be short-lived. These tokens are normally valid for hours or a day. As it has shorter period of time so it cannot be revoked instead we have to wait for this token to be timed out. Due to security best practices, if your’e using access token to access the secure APIs then make access token shorter time out. For example, time out can be an hour. It is used for authorization using OAuth2.0. Allow access to a resource such as API endpoint for CRUD operations.
    
2. **Refresh token**: Client uses refresh token to get access token. To get a refresh token, applications typically require confidential clients with authentication. This is much longer-lived; days or months or years. This can be revoked at any time.
    
3. **Session token**
    
4. **ID token:** It is used strictly used for authentication using OIDC
    
5. **CSRF token**
    

## Introduction

OAuth is a authorization framework which assists on authorization where as OIDC is for authentication. OIDC sits on top of OAuth 2.0 which in turn sits on top of HTTP.

OAuth 2.0 and OpenID Connect are different tools for different purpose.

## What is OAuth 2.0 ?

**At high level, OAuth is not an API or service but it’s an open source standard for authorization.**

OAuth is used for authorization. do not use it for authentication. It is used for delegated permissions. This is used when you want to provide access to certain resource to user like accessing profile picture, email, phone number etc.

## OAuth 2.0 roles

OAuth maintains several roles which help to facilitate and delegate access to protected resource.

1. **Resource Owner RO**: Entity that can grate access to a protected resource. This is a user, can be system or applicationThose who owns the data in the resource server. For example: I’m the resource owner of my Facebook profile.
    
2. **Client**: The application that wants to access a protected data on behalf of resource owner. A mobile app that wants to access a user's photos on a cloud storage service. ( eg: web or mobile app )
    
    1. A bit controversial, from auth server perspective, it is any app that tried to authenticate is client.
        
3. **Resource Server RS**: The server that hosts the protected resource. It verifies token and provides access to the resource. ( eg: server hosting file )
    
4. **Authorization Server AS**: Server responsible for authenticating the resource owner and obtaining their consent to gran† access to the client. Once its done, it issues access token to the client. ( eg: github, fb, linkedin they all operate their own authorization server )
    
5. **Authorization grant**: Credential representing the resource owner;s consent to allow the client to access their resource. There are differet tyoes of grant auth code, implicit, client credential etc
    
6. **Redirect URI:** After owner grants, authzn server redirects users back to client as a specific URL with authorization information. This is generally called `callback URL`
    
7. **Access token**: credential that represents that the authorization has been granted. They cannot do everything, only the things defined on the scopes
    
8. **Refresh token**: optional. used to get new access token incase previous one expired
    
9. **OAuth scopes**: It specifies the level of access that a client can be granted. It is maintained and defined in authorization server
    

## OAuth1.0 vs OAuth2.0

- simplicity: 1.0 was complex due to use of crypto signature on erach request
    
- support: 1.0 was designed fr server to server comm, but with different grant types, now we can support wider range of clients like mobile app, web app, IoT devices
    
- navite support: 1.0 was not well suited for native mobile apps, 2.0 introduced PKCE ( Proof key for code exchange ) to provide secure authorization
    
- token based: 1.0 was signature based auth, 2.0 used bearer token
    
- Scope: 1.0 did not have a standard way to maintain permission and scope, 2.0 introduces scopres allow for find grained access
    

## OAuth flows

### Auth code flow

**Usage**: Used by web app or server side app that can securely store client secrets ( When you control front channel and back channel )

**Steps**:

1. client redirects to resource owner to authzn server endpoint
    
2. resource owner authenticates and authorizer the client access request
    
3. Authorization serve responds with authzn code
    
4. client exchanges authorization code for an access token
    

### Implicit flow

**Usage**: suitable for client side app that cannot securely store client secrets ( When you have access of front channel only and not the back channel )

**Steps:**

1. client redirects to RO’s AS endpoint
    
2. RO authenticates and authorizes the client access request
    
3. AS responds with an access token
    

### Client credential flow

**Usage:** used for server-to-server communication ( Back channel only )

**Steps:**

1. client sends client credentials ( client ID and client secret ) to AS endpoint
    
2. AS verifies the creds and issues an access token
    

### Auth code with PKCE

Usage: Enhanced version of auth code to protect against attacks

Steps:

1. Same as auth code grant but includes a step to generate a challenge
    
2. Client generates one way hash
    
3. Challenge is used to prove the possession of the original code during token exchange
    

### Resource owner password grant flow

Usage: ( Back channel only )

## What is OIDC ?

OIDC is used for authentication. This is generally used in case when you just want to authenticate user. It is built on top of OAuth2.0. OIDC standarizes areas where Oauth2.0 leaves to choice such as scopre, endpoint etc

OIDC extends OAuth2.0

OpenID is a simple identity layer on top of the OAuth 2.0.

It allows clients to verify the identity of the End-User based on the authentication performed by an Authorization Server, as well as to obtain basic profile information about the End-User.

OIDC uses JSON Web Tokens (JWTs).

While OAuth is about resource sharing and accessing where as OIDC is about User Authentication.

It’s purpose is to give identity for multiple sites.

Every time you need to login into a website using OIDC is you are redirected to the OpenID site where you login, and again takes you back to the website. For example: if you chose to sign in with Google, firstly you will see the Google login form. When you are successfully logged in you are redirected back to your web app.

When you are redirected, you’ll get a secure JWT token.

## Where to use what ?

|   |   |   |
|---|---|---|
|**Use case**|**Identity**|**Type**|
|Simple login auth|OpenID Connect|Authentication|
|Single sign-on across sites|OpenID Connect|Authentication|
|Mobile app login|OpenID Connect|Authentication|
|Delegated authorization|OAuth 2.0|Authorization|

## Recommended flow types for use case

|   |   |
|---|---|
|**Use cases**|**Flow type to be used**|

|   |   |
|---|---|
|**Use cases**|**Flow type to be used**|
|Web app want to access API ( FE + BE )|- Authzn code flow<br>    <br>- Or hybrid ( implicit + code flow )<br>    <br>- You can also add refresh token flow<br>    <br>- machine to machine flow|
|Mobile apps|- Authorisation code flow with Proof Key for Code Exchange (PKCE)  <br>    Ref: [![](https://cdn.auth0.com/website/new-homepage/dark-favicon.png)Authorization Code Flow with Proof Key for Code Exchange (PKCE)](https://auth0.com/docs/flows/authorization-code-flow-with-proof-key-for-code-exchange-pkce)|
|SPA|- Authzn code grant flow|
|Mobile apps|- Authzn code flow<br>    <br>- Refresh token<br>    <br>- OAuth user/name pass directly ( legacy )|
|CLI apps or Daemons|- client credentials flow|
|API - To - API|- Client credential flow<br>    <br>- User will be in trusted systems|
|SSO with 3rd party service|Better integrate with OKTA / Auth0 / PingID|

Which OAuth2.0 flow should i use

Is client the RO ? Client credential flow

Is clienet web app runnig on the server ? Auth code grant flow

Is client Mobile app / native app ? Use Auth code with PKCE

Is client SPA ? Auth code with PKCE

Is client absolutely trusted with user credentials ? Resource owner password creds grant

## Reference

For debugging OIDC request

[![](https://oidcdebugger.com/favicon-16x16.png)OpenID Connect debugger](https://oidcdebugger.com/)

**JWT**

It consists of three parts ( Header, Payload, Signature )

Header:

Payload: sub, aud, iss, name

Signature: Hash

For inspecting JWT token

[![](https://jwt.io/img/favicon/favicon-16x16.png)JWT.IO](https://jwt.io/)




## References
1. 