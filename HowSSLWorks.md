# How SSL works
## 1. What is HTTPS and what does it do?

HTTPS takes the well-known and understood HTTP protocol, and simply layers a SSL/TLS (hereafter referred to simply as “SSL”) encryption layer on top of it. Servers and clients still speak exactly the same HTTP to each other, but over a secure SSL connection that encrypts and decrypts their requests and responses. The SSL layer has 2 main purposes:

1. Verifying that you are talking directly to the server that you think you are talking to
2. Ensuring that only the server can read what you send it and only you can read what it sends back

The really, really clever part is that anyone can intercept every single one of the messages you exchange with a server, including the ones where you are agreeing on the key and encryption strategy to use, and still not be able to read any of the actual data you send.

## 2. How an SSL connection is established

An SSL connection between a client and server is set up by a handshake, the goals of which are:

- To satisfy the client that it is talking to the right server (and optionally visa versa)
- For the parties to have agreed on a “cipher suite”, which includes **which encryption algorithm they will use to exchange data**
- For the parties to have agreed on any **necessary keys for this algorithm**

Once the connection is established, both parties can use the agreed algorithm and keys to securely send messages to each other. We will break the handshake up into 3 main phases - Hello, Certificate Exchange and Key Exchange.

- Hello - The handshake begins with the client sending a ClientHello message. This contains all the information the server needs in order to connect to the client via SSL, **including the various cipher suites and maximum SSL version that it supports**. The server responds with a ServerHello, which contains similar information required by the client, including a decision based on the **client’s preferences about which 'cipher suite' and version of SSL will be used**.

- Certificate Exchange - Now that contact has been established, **the server has to prove its identity to the client**. This is achieved using its SSL certificate, which is a very tiny bit like its passport. **An SSL certificate contains various pieces of data, including the name of the owner, the property (eg. domain) it is attached to, the certificate’s public key, the digital signature and information about the certificate’s validity dates**. (The client checks that it either implicitly trusts the certificate, or **that it is verified and trusted by one of several Certificate Authorities (CAs) that it also implicitly trusts**)[See section 3, on CAs].

- Key Exchange - The encryption of **the actual message data exchanged by the client and server will be done using a symmetric algorithm**, the exact nature of **which was already agreed during the Hello phase (the cipher suite)**. Both parties need to agree on **this single, symmetric key, a process that is accomplished securely using asymmetric encryption and the server’s public/private keys**.
(A symmetric algorithm uses a single key for both encryption and decryption, in contrast to asymmetric algorithms that require a public/private key pair)

    **The client generates a random key to be used for the main, symmetric algorithm. It encrypts it using an algorithm also agreed upon during the Hello phase (the cipher suite), and the server’s public key (found on its SSL certificate)**. It sends this encrypted key to the server, where it is decrypted using the server’s private key, and the interesting parts of the handshake are complete.
    
# Taken from / read more on: 
- http://robertheaton.com/2014/03/27/how-does-https-actually-work/
