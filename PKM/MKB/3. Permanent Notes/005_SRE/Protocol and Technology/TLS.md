
Gold: https://tlseminar.github.io/


TLS
X.509 standard for PKI

#### TLS handshake

- Client hello ( Client random, list of cipher suits, TLS versions supported)
- Server hello ( Server random, chosen cipher suits, TLS version, Server Public certificate)
- Client verified certificate authenticity with CA
- Client computes Pre-Master Secret ( pseudo-random bytes )
- Client sends PMS encrypted with server pub cert
- Server decrypts PMS with its private key
- Client and Sever Generate Session keys ( symmetric key ) using PMS + CR + SR
- Client sends ChangeCipherSpec and
- Client Sends Finished message encrypted with session keys
- Server sends ChangeCipherSpec 
- Server sends Finished message encrypted with session keys


### DH key exchange
- instead of client sending the PMS over, it is calculated separately

### EDH key change
- different 

### TLS 1.0 vs 1.2 vs 1.3
- More channels
- 0-RTT
- Faster handshake
- Protection from downgrade attacks and MITM ( FREAK, POODLE
- )


### 0-RTT handshake
- If connection has been established before, it reuses the same to generate session key and s


#### TLS forward secrecy ( PFS )
- Making sure 
- Heartbless vulnerability - 64K required by server but attacker client sent smaller packet, in order to fulfil, server responsed with by padding the data from its memory.
- Using DH or EDH

#### TLS false start
- Generally handshake is done in TLS and then data is transmitted.
- This can slow down and affect performance.
- Using TLS false start, before sending `changecipherspec` and `finished`, data is sent