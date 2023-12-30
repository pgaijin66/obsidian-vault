
Json web token

### JWT is not used for authentication, it is authorization

header.payload.signature
https://jwt.io

example token
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

![[Screenshot 2023-05-21 at 9.53.24 AM.png]]'



HS256 = [[Braindump/HMAC]] +  [[SHA265]]

### Issues with JWT
- A lot of crypto to choose from ( confusing )
- There is a saying there are two hard things in comp science: cache invalidation, and naming things.
- JWT (JSON Web Tokens) is named well but fails at the problem of cache validation or user invalidation.
-   Logging out the user with JWT is not possible since the server cannot ensure the token is forgotten by the client software.
-   One approach is to maintain a list of invalid tokens, but this leads to checking the token on every request and loses the advantage of JWT over simpler bearer tokens.
-   Another strategy is to issue a short-lived JWT and a long-lived refresh token. When JWT expires, the client uses the refresh token to request a new JWT.
-   However, this reintroduces the use of bearer tokens and adds complexity to the system.
-   JWTs have additional problems like increased attack surface due to JSON parsing/validation complexity, misconfigurations allowing no signature, data leakage, and poor implementation of API clients.
-   The issues with JWTs are numerous and extensive.

### Stateless JWT

### Stateful JWT


Do not use JWT for sessions


### PASETO ( Platform Agnostic Security Token)

- strong algo out of the box ( opionated )
- Two version version 1 and version 2
- For symmetric algo, it uses [[ChaCha20]] with [[Poly1305]]
- When combined, ChaCha20 and Poly1305 provide a secure and efficient way to encrypt and authenticate data. ChaCha20 takes care of scrambling the message, while Poly1305 ensures that the message hasn't been tampered with during transmission. This combination is commonly used in modern cryptographic protocols to provide confidentiality and integrity for sensitive information, such as secure communication over the internet or protecting data on disk.
-  for a public asymmetric-key scenario, Edward-curve digital signature algorithm with curve `25519` is used.
- No algo header, hence attacker cannot change the algo for forge token
- Everything in token is authenticated using [[AEAD]]
- the payload is now encrypted, not just encoded
- 