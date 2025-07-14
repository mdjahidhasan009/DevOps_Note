## How SSL/TLS Secure data?

Encrypt data and ensure its
*   integrity,
*   confidentiality, and
*   authenticity

between a client and server. And it can be between two servers as well or client to load balancer.

* SSL refers to Secure Sockets Layer, an older protocol, used to encrypt connection.
* TLS refers to Transport Layer Security, the modern version of SSL, which is more secure and efficient.
* Nowadays, TLS certificates are mainly used, but people still refer to them as SSL certificates.
* Public SSL certificates are issued by Certificate Authorities (CAs) to verify the identity of websites and enable 
  secure, encrypted communication over the internet like Comodo, Symantec, DigiCert, GoDaddy, GlobalSign, DigiCert, 
  Letsencrypt, etc.
* SSL certificates have a expiration date must be renewed periodically to maintain security.

### Security Concepts Analogy: Exam Papers

This illustrates the core principles of secure communication using the analogy of sending exam papers to an exam center.

*   **Confidentiality:** Paper is only accessed by exam center.
*   **Integrity:** Paper is not modified in between.
*   **Authentication:** Verifying the identity of the parties who they are supposed to be.

*(Diagram shows Question Papers being sent securely to an Exam Center where a person's identity is verified.)*

### How Technology Achieves Security

*   **Confidentiality:** Data is only accessed by client/server -> **Encryption**
*   **Integrity:** Data is not modified in between -> **Hashing**
*   **Authentication:** Verifying the identity of the parties who they are supposed to be. -> **Certificates**

### Encryption

Converting plaintext information into a coded form (cipher text).

**Simple Example (Caesar Cipher):**
`ABCDEFGHIJKLMNOPQRSTUVWXYZ`

**Encrypt** `DEMO` --> `GHPR`
*(Ciphertext, each character shifted by 3 char)*

*   **Encrypt:** `DEMO` --> `GHPR` (Shift each char by 3 forward)
*   **Decrypt:** `GHPR` --> `DEMO` (Shift each char by 3 backward)

### Types of Encryption

Encryption can be divided into two main types:
*   Symmetric
*   Asymmetric

#### Symmetric Encryption
The same key is used for both encryption and decryption.

*   **Encrypt** (key=3): `DEMO` --> `GHPR` (Shift each char by 3 forward)
*   **Decrypt** (key=3): `GHPR` --> `DEMO` (Shift each char by 3 backward)

#### Asymmetric Encryption
Different keys are used for encryption and decryption.

*   **Encrypt** (Key is 3): `DEMO` --> `GHPR` (Shift each char by 3 forward)
*   **Decrypt** (Key is 23): `GHPR` --> `DEMO` (Shift each char by 23 forward)

This uses a key pair:
*   **Encrypt Key is 3** -> Public Key
*   **Decrypt Key is 23** -> Private Key

### The TLS Handshake

The client/browser and server perform a "handshake" to agree on security parameters and exchange keys before sending 
data.

*(Diagram shows a client/browser at banking.com initiating a handshake with a Banking Website server.)*

#### Asymmetric Encryption in Action

*(Diagram shows a client using a Public Key (key=3) to encrypt "DEMO" into "GHPR". The data is sent to the server, which uses its Private Key (key=23) to decrypt "GHPR" back into "DEMO".)*

### Symmetric vs Asymmetric

**Symmetric:**
*   Faster and more efficient, suitable for large data.
*   Challenging key distribution, single key must be kept secret.

**Asymmetric:**
*   Slower, suitable for small data.
*   Simplified key distribution, secure even if the public key is shared.

### Hybrid Encryption

Combines the strengths of both symmetric and asymmetric encryption to achieve efficient and secure communication.

**Asymmetric encryption** is used to securely exchange a symmetric key between parties.

Once the symmetric key is securely exchanged, it is used to encrypt and decrypt the actual data.
*   **Symmetric encryption** is faster and more efficient, making it ideal for encrypting large amounts of data.

### Some Algorithm Examples

| Asymmetric | Symmetric |
| :--------- | :-------- |
| • DSA      | • AES     |
| • RSA      | • 3DES    |
| • ECC      | • RC4     |
| • ECDH     |           |

### How real life AES encryption looks like...

*   **Message:** `DEMO`
*   **Key 256 bit HEX:** `cb5a6cefcf5b7e88f9bff6f27f32d6095a86db829d8518cf8edb6af2740ff8eb`
*   **Initialization Vector (IV):** `a8d246bb6ebae2b0e7651843b3053384`
*   **AES-256-CBC Encryption:** `8?Q3?B??Z)?07h??`

## Hashing

Hashing is the process of converting data into a fixed-size string of characters, as a sequence of numbers and letters.

**Example:** `DEMO` --> `37` (4 + 5 + 13 + 15 = 37)
*(Using A=1, B=2, ...)*

### MESSAGE AUTH CODE (MAC)
Combining `MESSAGE + CODE`

*   **Message:** `DEMO`
*   **Secret Key:** `key123`
*   **Hashed Output:** `DEMOkey123` --> `84`

### Most Common Hashing Algos

*   MD5 (Message Digest Algorithm 5) (128bits)
*   SHA (Secure Hash Algorithm)
    *   sha-1
    *   sha-2/3 (224 256 384 512)

**Hash-based Message Authentication Code (sha256hmac)**



## Certificates

### Certificate Authority (CA)
A Certificate Authority (CA) is a trusted organization that issues digital certificates to verify the identity of websites and enable secure, encrypted communication over the internet.

CAs ensure the authenticity and integrity of the SSL certificates they provide.

### Formats for digital certificates

| Format   | Encoding         | Common Extensions    | Usage                          | Contains Private Key           |
|:---------|:-----------------|:---------------------|:-------------------------------|:-------------------------------|
| PEM      | Base64           | .pem, .crt, .cer     | Web servers, email             | No (unless it's a key file)    |
| DER      | Binary           | .der, .cer           | Java platforms, binary data    | No                             |
| PKCS#7   | Base64 or Binary | .p7b, .p7c           | Certificate chains             | No                             |
| PKCS#12  | Binary           | .p12, .pfx           | Export/import certs with keys  | Yes                            |



## The SSL/TLS Handshake Process

1.  **Client Hello**
    *   Client: "Hello, I support these SSL/TLS versions and cipher suites..."
2.  **Server Hello**
    *   Server: "Hello, I choose this SSL/TLS version and cipher suite..."
3.  **Server Certificate**
    *   Server: "Here is my certificate with my public key..."
4.  **Certificate Verification**
    *   Client: "I verify your certificate..."
5.  **Key Exchange**
    *   Client: "I encrypt a pre-master secret with your public key..."
    *   Server: "I decrypt the pre-master secret with my private key..."
6.  **Session Key Generation**
    *   Both: "We generate a session key using the pre-master secret..."
7.  **Client Finished**
    *   Client: "Handshake complete (encrypted with session key)..."
8.  **Server Finished**
    *   Server: "Handshake complete (encrypted with session key)..."
9.  **Secure Communication**
    *   Both: "Let's communicate securely using the session key..."


# Resources
* [AWS in ONE VIDEO 🔥 For Beginners 2025 [HINDI] | MPrashant](https://www.youtube.com/watch?v=N4sJj-SxX00)
* [Ultimate AWS Certified Solutions Architect Associate 2025](https://www.udemy.com/course/aws-certified-solutions-architect-associate-saa-c03)