---
id: 8a2e1256-b9d1-47f1-862a-95443cc7cf31
---

# mTls in 15 minutes | ITNEXT
#Omnivore

[Read on Omnivore](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9)
[Read Original](https://itnext.io/mtls-in-5-10-okay-20-minutes-6602eddae6fe)

## Highlights

> TLS [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#de4224d0-663f-4f3b-a92b-92b02658f1de) 

> TLS is really asymmetric cryptography in a trench coat. [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#fb1a4eb5-4717-4081-9fc7-78efb54d2f34) 

> ## Asymmetric Cryptography
> 
> An asymmetric cryptographic algorithm (also called public/private key cryptography) will provide you with a public and a private key. [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#452bb959-3ae2-46b5-addc-503bb7eee802) 

> TLS is really asymmetric cryptography in a trench coat. To understand this whole thing you do need a high-level understanding of asymmetric key cryptography. Sounds scary, but trust me it’s not that complicated (well, at least all of the complicated parts are abstracted away neatly for us).
> 
> ## Asymmetric Cryptography
> 
> An asymmetric cryptographic algorithm (also called public/private key cryptography) will provide you with a public and a private key. RSA is an example of this algorithm. Commandline tools like [openssl](https://www.openssl.org/) are a handy way to utilize these algorithms in practice. [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#b46eeb6d-c4d5-4eb9-98b2-f710e03a3fa2) 

> RSA is an example of this algorithm. Commandline tools like [openssl](https://www.openssl.org/) are a handy way to utilize these algorithms in practice. [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#2dd47ff5-043b-4f6a-af96-470de2b9ebe4) 

> public keys are given out freely. They allow anyone to encrypt something and send that message to the private key holder. [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#defb938a-fd59-4530-ad93-cd8fd9951452) 

> ## Certificate Authorities
> 
> Asymmetric Cryptography is most of what enables TLS. The next part is a Certificate Authority. Certificate Authorities are really just another clever implementation of asymmetric cryptography. There’s definitely a rectangle-is-a-square thing going on here.
> 
> A public/private key will tell you who I am and allow you to communicate with me securely… but who the hell am I _anyway?_ Sure — I’m Steven, a software engineer that likes to write things online, but why should you care?
> 
> It’s almost like we need some kind of _certificate_ of _authority_ that lets you know, not only that I am who I say I am, but that I’m someone worth listening to. A “blue-check”, if you will.
> 
> This is where Certificate Authorities come into play. CAs tell you: “this guy is actually _google.com_ so listen up”. When you purchase a web domain, you can request a certificate from a CA that indicates: “I own this domain” (or, more specifically, the entity that holds the private key for this domain owns this domain).
> 
> ![](https://proxy-prod.omnivore-image-cache.app/700x382,s9qxqxcZCjIigwLBAH0b6poaCbRyAhFla_3aQCrWbJqk/https://miro.medium.com/v2/resize:fit:700/1*GlKlvuQsiMFWtweD3s-IQg.jpeg)
> 
> Certificate Authorities are institutions set up to verify web domain ownership, which enables encryption online. [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#a746d5ad-edb4-47cb-a25b-4ea0d6c3579d) 

> Certificate Authorities are institutions set up to verify web domain ownership, which enables encryption online. They’re set up by people that have an interest in maintaining the function of the Internet. Sometimes they’re private companies, like GoDaddy. Other times they’re non-profits set up by nerds, like [letsencrypt](https://letsencrypt.org/).
> 
> But, what they really are is a private key. A reaaaally secret one. [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#540ea18c-d1a6-4e33-b70a-3a9ed3613f8a) 

> the certificate verifies that the public key is issued by the owner of the website. And, as we previously went over, public keys are tied to a _single_ private key. which means that if you can prove you have that private key, you are who you say you are. Verifying that the server actually has that private key is accomplished by encrypting a message with the public key which that server (the only holder of the private key) can then decrypt. [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#695d672e-462a-4995-979e-299ca7ca461f) 

> First, your browser doesn’t actually reach out to a certificate authority for its public key. Your laptop/computer actually comes with CA public keys for most of the trusted CAs that exist. Second (and feel free to ignore this part), the message that’s encrypted at the end isn’t actually sent back as is. [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#c7ac64b0-7ea9-403a-8826-0353e8e2a524) 

> The last message sent by the web browser is actually a random value provided by the browser as a “salt” for a different, symmetric encryption algorithm. [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#826061d9-d131-4f6a-a516-4108bbd8508d) 

> That value is called a “session key”. What the server sends back is a different message encrypted using the session key and the symmetric algorithm. [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#708c5ee2-4fbf-408b-831c-9631e5b1b904) 

> The fact that the browser can then decrypt that message proves the server has the session key, and was, therefore, able to decrypt it using its private key. [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#9af31516-7fbb-4373-8f69-e7ec82dacba6) 

> ## Curl
> 
> To send a request to that server, you might use curl:
> 
> curl https://www.mysite.com 
> 
> Pretty simple now that we know how TLS works, right? But what happens when a site isn’t secure — i.e. the certificate is invalid? Curl will give you an error if you attempt to connect to a website over https. Well, you can turn off certificate validation using `-k` , like so:
> 
> curl -k https://www.mysite.com
> 
> This ^ will become important in a bit, I promise.
> 
> ## mTLS
> 
> So, we’ve set up a TLS connection. Now, how do we make this an _m_TLS connection? Like this -
> 
> curl https://www.mysite.com --cert client.crt --key client_private.key
> 
> What makes this mTLS? Well, as you saw in the previous section, you don’t need to send anything additional to the server to make a TLS connection. The client sends nothing and validates what the server sends back. [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#b509eccf-5bd1-4e5d-aff3-627f1c8a376d) 

> ## mTLS
> 
> So, we’ve set up a TLS connection. Now, how do we make this an _m_TLS connection? Like this -
> 
> curl https://www.mysite.com --cert client.crt --key client_private.key [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#972a69b1-2059-4b5c-8412-c1457b7f61e4) 

> Here, the client (the one initiating the connection) is sending a certificate _as well_ and also specifies a private key. This isn’t necessary for a TLS connection. So, whenever you see a client sending a certificate, you know it’s mTLS.
> 
> Sending a certificate prompts the server to also verify the client’s cert. The server side remains unchanged. However, you might also add a CA certificate, using `ca-file` , like so:
> 
> frontend www.mysite.com  
>     bind 10.0.0.3:80  
>     bind 10.0.0.3:443 ssl crt /etc/ssl/certs/mysite.pem ca-file /etc/ssl/ca.crt  
>     default_backend web_servers [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#e1860b93-5e8a-4b55-afd2-5d1dad44c9ee) 

> You might do this because, in many mTLS scenarios, entities use private certificate authorities. This tells HA Proxy _how_ to validate the incoming certificate. The OS of the HA proxy server also might not come with any CA certificates installed because it isn’t necessary for plain TLS. [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#e44170df-8478-42a9-b2ed-0e94b6ff7de8) 

> ## Pop Quiz
> 
> Given an mTLS connection initiated by curl, what happens when `-k` is used, like so:
> 
> curl -k https://www.mysite.com --cert client.crt --key client_private.key
> 
> Which sides of the request, if any, are still validated? I’ll include the answer in a comment. [⤴️](https://omnivore.app/me/m-tls-in-15-minutes-itnext-188a8e78ab9#ebcfb43b-eee9-4741-90dd-22ca579069b0) 

