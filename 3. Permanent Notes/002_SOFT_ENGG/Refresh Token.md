
1.  It is up to a client to handle when their access code expires; some will check the expiration date themselves and pre-emptively use the refresh token to fetch a new access token, others will try to get a new access token when they get a http 400 error. Either way, if you give the client an access- and refresh token, it’s up to them to renew their access token. The refresh token is there so that the user isn’t bothered by the client application to log in again.
2.  Personally I’d opt for a cookie marked as ‘secure’: [https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#restrict_access_to_cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies#restrict_access_to_cookies). Ideally it would even be marked as `HttpOnly`, but that would mean your web client’s Javascript code cannot access it; in that case, you’d need a server-side component that can renew the access token if needs be, but also to handle things like logout to delete the cookie. Local storage is more vulnerable to e.g. XSS attacks.
3.  Your server-side implementation will have a list of all currently active access and refresh tokens; just delete them server-side and you have revoked them. The client still has them but they’re useless.

2- Always server side...  Take for granted that anything client-side will be hacked (easily)


use server side http sessions that are backed by session id passed through http only cookie. the problem is CSRF attack.  

You would need to generate CSRF tokens and validate it on every request but it has it’s own challenges lol. It is just an extra set of not-100% bulletproof block which will help but won’t guarantee.Sending user and password to api to receive access token and then storing accessing in browser local in local storage. Vulnerable to XSS attack.  

All you can try here to focus efforts on blocking XSS attacks [https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)Third option is like you said storing refresh token in http only cookie and access token in browser storage  

Access token will be vulnerable to XSS attacks  

And refresh token vulnerable to CSRF.Fourth option is storing refresh token in backend. But it’s similar to option 3. It could be as simple as sending a request to backend and the attacker has access to a new access token.One thing that compliments all of the above is CORS. 

The other websites won’ be able to simply send a request and refresh access tokens.Btw third party cookie are being banned very very soon which will help tighten things up but it will also generate headache to certain products related to tracking and marketing.