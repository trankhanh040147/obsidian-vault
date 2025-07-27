# Public Key Cryptography (asymmetric cryptography)
- Public key cryptography (also called **asymmetric cryptography**) is a type of encryption that uses two different keys: a **public key** and a **private key**. These keys are mathematically related, but it’s practically impossible to reverse the relationship between them (i.e., you can't figure out the private key from the public key).
Here’s how it works:

- **Public Key**: This is the key you can share openly with anyone. You can think of it like a locked box that anyone can put a message into, but no one can open it.
    
- **Private Key**: This is the key you keep secret. Only you have this key, and it’s used to unlock messages that were locked with the corresponding public key.
    

So, the idea is:

- If someone wants to send you a private message, they use your **public key** to encrypt the message.
    
- Once the message is encrypted, only you can decrypt it using your **private key**.
    

This is why it’s called "asymmetric" — the encryption and decryption happen with different keys.

---
# Secure key exchange
A **key exchange** is a method that allows two parties (like computers or users) to share a secret key, which they can use for encrypted communication. The key must remain secret, even though the parties might be communicating over insecure channels (like the internet).

However, the challenge is that if both parties are sending their secret keys to each other directly, someone might intercept the key in transit. This is where **secure key exchange** protocols come in — they allow two parties to share a secret key safely, even if the communication happens over an insecure channel.

The process often works like this:

- Both parties use a public key cryptography method (like Curve25519) to **generate a shared secret** without actually sending the secret over the network.
    
- Even though attackers might be eavesdropping on the communication, they cannot figure out the shared secret because they can’t easily reverse the encryption.
    

In simple terms, **secure key exchange** helps two parties agree on a secret key that only they know, even if someone is listening to their conversation.

---
# Format 
- PEM
PEM (Privacy-Enhanced Mail) is a container format used for encoding cryptographic keys, certificates, and other data. It is typically Base64-encoded and wrapped in **"BEGIN" and "END" tags**. For example:

- **Private Key**: `-----BEGIN PRIVATE KEY----- ... -----END PRIVATE KEY-----`
    
- **Public Key**: `-----BEGIN PUBLIC KEY----- ... -----END PUBLIC KEY-----`
    

This is the format used to store keys and certificates in many cryptographic systems, including **RSA**, **DSA**, **ECDSA**, etc.

---
# Curve25519
### Form of the Curve25519 Private Key

1. **Length**: The private key for **Curve25519** is always a **32-byte** value (256 bits).
    
2. **Randomized Value**: The private key is a random 32-byte number that must be generated in a cryptographically secure way.
    
3. **Encoding**:
    
    - **Raw form**: A 32-byte raw binary string.
        
    - **Base64 or Hex encoding**: When transmitting or storing the private key, it is often encoded in **Base64** or **Hex** for easier handling.
        
    
    For example:
    
    - **Raw private key**: A 32-byte string (e.g., `b'\x72\x84\x58\x3a\x4c\x2a\x77\x2b\xfa\xa2\x19\x5c\xcf\xe9\x90\x48\xcd\x1a\xe3\x74\x36\x2b\x4a\x56\x39\xfe\x7d\xb7\xde\x55\x4b'`)
        
    - **Base64 encoded private key**: `dLkl2hJj8HTybUlCT6kqZx9u7pOfuRU24H1m2u27r9d`
        
    - **Hex encoded private key**: `7284583a4c2a772bfaa2195ccfe99048cd1ae374362b4a5639fe7db7de554b`
        

### Private Key Restrictions

For **Curve25519**:

- **Private Key Constraints**: There are some restrictions on the valid range of private keys for **Curve25519**:
    
    - The private key must be **between 0 and 2^252 - 1** (this range is due to the characteristics of the curve).
        
    - Typically, a **clamping** operation is applied to the private key before it's used. This involves:
        
        - Setting certain bits to ensure the private key is in the valid range.
            
        - Preventing certain predictable patterns to increase security.
            
    
    This clamping step ensures that the private key is a valid and secure one for use in the key exchange.