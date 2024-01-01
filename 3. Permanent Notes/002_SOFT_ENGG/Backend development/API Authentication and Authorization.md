
Access token

Refresh token

access token valid 15 min  
refresh token 24 hr

if access token expired then refresh token is sent to that enpoint to get new access tokengenerate both againpically for JWTs you'll have an access token, that's valid for ~15 minutes, and a refresh token that is valid for longer (e.g. 24 hours).

To access API end points, the browser sends only the access token. 

If it receives a [[401]] HTTP status, it then refreshes it's token by submitting the refresh token to a specified end point, retrieves two new tokens (both refresh and access) and continues along.Here's the wonderful thing. 

Both tokens have expiry dates, both tokens are signed -- hence you should always validate all tokens (regardless of access or refresh) using the same logic, before performing token specific validation.


## Use middleware

- [[Authentication middleware]]
- [[Authorization middleware]] or function

## Methods

`IsAuthenticated`
`IsAuthorized`
`RequireAuthentication` - [[middleware]]


## Tags

#api #security