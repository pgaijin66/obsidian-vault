
# Generate certificates
[[openssl]] can be used to generate self signed certificate


> [!NOTE] `openssl req` creates keys in legacy RSA format, where as `openssl genrsa` to generate key in standard [[PKCS]] format ( specifically PKCS#8)
 

```bash
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.prm -sha256 -days 365
```


> [!NOTE] If you do not want to secure it with passphrase
> If you do not want to use passphrase, you can use option such as `nodes` short for no DES


https://www.openssl.org/docs/manmaster/man1/req.html

#### All in one command
```shell
openssl req \
	-x509 \
	-newkey rsa:4096 \
	-sha256 \
	-days 3650 \
	-nodes \
    -keyout example.key \
    -out example.crt \
    -subj "/CN=example.com" \
    -addext \ "subjectAltName=DNS:example.com,DNS:www.example.net,IP:10.0.0.1
```

## Be careful of followings
- Proper crypto eg: use [[rsa]] eg:  `rsa:4096` and and [[sha]] eg: `sha256` ( sane values as of 2023 )