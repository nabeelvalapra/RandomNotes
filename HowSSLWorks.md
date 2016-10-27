# How HTTPS works.
- HTTPS provide a Secure Socket Layer over HTTP protocol.
- Verifies that you are talking to the server you think you are.
- Ensures that only that server can read the data you are sending.

## How SSL connection is established.
- SSL connection is established via handshake.
- Goals of the SSL handshakes:
    - To satisfy the client is talking to the right server.
    - To securly create a session-key(symmetric-key) within client and securely pass it to the server. And use this key for the rest of the session.
- Steps in the SSL handshake process:
    - ClientHello:
        - Information that the server needs to communicate with the client using SSL.
        - Including SSL version number, cipher settings, session-specific data.
    - ServerHello:
        - Information that the client needs to communicate with the server using SSL.
        - Including SSL version number, cipher settings, session-specific data.
        - Including **Server’s Certificate** (includes server's Public Key, **Certificate Autority**, Digital Signature, Certificate Validity Date).
    - Authentication and Pre-Master Secret:
        - Client authenticates the **Server's Certificate**. (e.g. Common Name / Date / **Certificate Authority**)
        - Client (depending on the cipher) creates the pre-master secret for the session.
        - Encrypts with the server's public key and sends the encrypted pre-master secret to the server.
    - Decryption and Master Secret:
        - Server uses its private key to decrypt the pre-master secret.
        -  Both Server and Client perform steps to generate the master secret with the agreed cipher.
    -Generate Session Keys:
        - Both the client and the server use the master secret to generate the session keys,  which are symmetric keys used to encrypt and decrypt information exchanged during the SSL session.
    - Encryption with Session Key:
        - Both client and server exchange messages to inform that future messages will be encrypted.

## Certificate.
- Why trust a certificate?
    -   The SSL Certificate is a simple text file, so its easy to generate and anyone can modify it. So if a client asks for verfication, the attacking-server can send a fake-certificate (with modified keys and details and  public-key of this fake-server).
    -   This why you need a Digital-Signature of a third-party (trust-party/Certificate Authority) which allows a client to verify that this-server's a Certificate really is legit.
    -   There are 2 sensible reasons why you might trust a certificate:
        - If it’s on a list of certificates that you trust **implicitly**. So a list of Certificate from different CA's will be pre-installed to the client. (Root CAs)
        - If it’s able to prove that it is trusted by the controller of one of the certificates on the above list. (Intermidiate CAs)
    - There are three/four leading CA like Symantec, Comodo, GoDaddy, etc. These CAs will verify that server is legit and the purpose of the server/domain. If the domain is 'm1crosoft' the CA will reject the application.
- How Digital Ceritificates works?
    - We know the public/private key asymmetric algorithm used in SSL Handshake. The private key is secured and public key is distributed. On private-key can distribute the public-key encrypted contents.
    - The opposite is true for digital signature. The certificate-content is `MD5 hashed and signed  by CA's private-key` and anyone can decrypt the cerificate-content who has the `CA's public-key` (and the client's will have the CA's public-key). This way you can trust the ssl-cerificate provided by the server and the server itself is legit.
    - So if a server comes claiming it controls the microsoft.com domain. Its certificate will be signed by a CA's private-key. We decrypt the SSL-cerificate with CA's public-key which available with the client.
- Self-signing
    - All root CA certificates are “self-signed”, meaning that the digital signature is generated using the certificate’s own private key.
    - There’s nothing intrinsically special about a root CA’s certificate - you can generate your own self-signed certificate and use this to sign other certificates (Intermidate CAs) if you want.
    - But since your random certificate is not pre-loaded as a CA into any browsers anywhere, none of them will trust you to sign either your own or other certificates.

# Taken from / read more on: 
- http://robertheaton.com/2014/03/27/how-does-https-actually-work/
- https://www.symantec.com/connect/blogs/how-does-ssl-work-what-ssl-handshake
- https://www.youtube.com/watch?v=hH69NWUv2Cs
- https://www.youtube.com/watch?v=DP5jdJB_TgY
