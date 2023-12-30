# Authenticated Encryption with Associated Data (AEAD)

https://developers.google.com/tink/aead

It is a cryptographic construction that combines both encryption and authentication in a single operation. AEAD algorithms provide confidentiality, integrity, and authenticity of data, ensuring that the data remains private and unaltered during transmission or storage.

AEAD schemes are designed to protect the confidentiality of sensitive information while also verifying its integrity. They take plaintext data, a secret key, and associated data (optional additional information) as input and produce ciphertext as output. The ciphertext provides confidentiality by obscuring the original data, and it also includes an authentication tag that ensures the integrity and authenticity of the data.

The authentication tag allows the recipient to verify that the ciphertext has not been tampered with or modified in any way. It provides protection against unauthorized modifications, replay attacks, and other forms of tampering.

AEAD algorithms are commonly used in various security protocols and applications, such as secure communication protocols (e.g., TLS/SSL), disk encryption, wireless protocols, and secure messaging systems. They offer a convenient and efficient way to provide both encryption and authentication, reducing the complexity and potential vulnerabilities of using separate encryption and authentication mechanisms.