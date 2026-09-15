---
title: Cryptography & Hashing
parent: Writeups
nav_order: 3
---

<h1 style="color:#33aaff;">Cryptography & Hashing with OpenSSL</h1> 

<br>

<h2>Objective</h2>


**Cryptography is a foundational component of cybersecurity** that protects the confidentiality, integrity, and authenticity of information. It powers secure websites like HTTPS, protects passwords, enables encrypted messaging, and forms the backbone of technologies like blockchain and digital identity. This lab introduced core cryptographic concepts using OpenSSL, including hashing, symmetric encryption, and asymmetric encryption. The objective was to demonstrate how cryptography protects confidentiality, integrity, and secure communication through SHA-256 hashing, AES-256 encryption, and RSA key pairs. 

<br>

---

<h2>Environments & Tools Used</h2>

| Category | Items | 
| :--- | :--- | 
| Operating System | Ubuntu | 
| Tools | OpenSSL, Nano text editor, Linux command-line utilities | 
| Cryptographic Technologies | SHA-256 hashing, AES-256 symmetric encryption, RSA asymmetric encryption | 

<br> 

---

<h2>Step-by-Step Analysis</h2>

### Part A: Hashing with SHA-256

1. Created a Test File:
   
``` bash
echo “This is my first Cybersecurity Lab” > file1.txt 
```

*Verified file creation:* 

``` bash
ls -l file1.txt
To show file creation, size, permissions, owner, and timestamp.

cat file1.txt
To confirm that the plaintext string was written correctly to the storage block.
```

<br>

2. Generated a SHA-256 Hash for the File’s Contents:

``` bash
openssl sha256 file1.txt
```
*Observation:* A unique SHA-256 digest was generated, serving as the file's digital fingerprint. Any modification to the file would result in a completely different hash value. 

<br>

3. Verified Hash Consistency:
   
*Created a duplicate file:* <br>

``` bash
cp file1.txt file2.txt
```

*Hashed the copied file:* <br>

``` bash
cp file1.txt file2.txt
Created an exact copy of your file with a new filename.

openssl sha256 file2.txt
Generated a SHA-256 hash for this new file’s contents. 
```
*Observation:* The hash output for `file1.txt` and `file2.txt` matched because the file contents were identical. Hash functions operate on content rather than filenames or metadata. 

<br>

4. Demonstrated Hash Sensitivity:

*Modified the original file by adding a single period (.):* <br>

``` bash
nano file1.txt
Added a (.) at the end of the sentence.
```

*Re-generated the hash* <br>

``` bash
openssl sha256 file1.txt
```

*Observation:* The new hash differed from the original despite only one character being changed. This demonstrates the avalanche effect and hash sensitivity. 

<br>

--- 

### Part B: Symmetric Encryption with AES-256 

1. Created a Sensitive File:

``` bash
echo “This is top secret information.” > secret.txt 
```

<br>

2. Encrypted the File Using AES-256-CBC

``` bash
openssl enc -aes-256-cbc -pbkdf2 -salt \ -in secret.txt \ -out secret.enc

openssl enc = Tells OpenSSL to use its encryption module
-aes-256-cbc = Specifies the encryption algorithm
-pbkdf2 = Uses a secure password-based key derivation function
-salt = Adds randomness making it harder to brute-force
-in secret.txt = The input file you want to encrypt
-out secret.enc = The output file that will contain the encrypted version

Prompted to enter a password. This password is used as the encryption key.
```

<br>

3. Decrypted the File:

``` bash
openssl enc -d -aes-256-cbc -pbkdf2 \ -in secret.enc \ -out decrypted.txt

Use the same password that was used during encryption.
```

*Verified contents:* <br>

``` bash
cat decrypted.txt
```

*Result:* 

``` bash
This is top secret information.
``
```

*Observation:* AES uses a shared secret key for both encryption and decryption. Successful recovery of the plaintext demonstrated how confidentiality depends on protecting the encryption key. 

<br>

---

### Part C: Asymmetric Encryption with RSA 

1. Generated an RSA Key Pair:

*Private Key:* 

``` bash
openssl genpkey -algorithm RSA \ -out private.pem \ -pkeyopt rsa_keygen_bits:2048

openssl genpkey = Uses OpenSSL’s key generation tool
-algorithm RSA = Specifies the RSA algorithm 
-out private.pem = Saves the generated private key to the file named private.pem
-pkeyopt rsa_keygen_bits:2048 = Sets the key size to 2048 bits, the common secure default
```

*Then Extracted the Public Key:* 

``` bash
openssl rsa -pubout \ -in private.pem \ -out public.pem

openssl rsa = Uses OpenSSL’s RSA key management tool
-pubout = Tells OpenSSL to output the public key from the private key
-in private.pem = Specifies the input file containing the RSA private key 
-out public.pem = Specifies the output file to save the extracted public key
```

<br>

2. Encrypted a Message Using the Public Key:

``` bash
echo “RSA encryption in action!” > message.txt 
```

<br>

3. Encrypted with Public Key:

``` bash
openssl pkeyutl -encrypt \ -pubin \ -inkey public.pem \ -in message.txt \ -out encrypted.bin

openssl pkeyutl = Uses OpenSSL’s public/private key utility tool
-pubin = Tells OpenSSL that the input key (-inkey) is a public key
-inkey public.pem = Points to the public key file used for encryption
-in message.txt = Specifies the plaintext file to encrypt 
-out encrypted.bin = Specifies the output file for the encrypted data

```

<br>

4. Decrypted the Message with the Private Key:

``` bash
openssl pkeyutl -decrypt -inkey private.pem -in encrypted.bin -out decrypted.txt 
```

*Displayed the Message:* 

``` bash
cat decrypted.txt
```

*Result:* 

``` bash
RSA encryption in action!
```

*Observation:* The message could only be decrypted using the corresponding private key, demonstrating how public/private key pairs provide secure trust-based communication. 

<br>

---

<h2>Key Findings & Artifacts</h2>

<u>Hashing</u> <br> 

* SHA-256 produces a unique digest for file content.
* Identical files generated identical hashes.
* A one-character modification produced a completely different hash.
* Demonstrated integrity verification and collision resistance principles.

<u>AES Encryption</u> <br> 

* Successfully encrypted plaintext into ciphertext.
* Data was recoverable only with the correct password.
* Confidentiality depends on proper key management.

<u>RSA Encryption</u> <br>

* Generated a 2048-bit public/private key pair.
* Public key encrypted data.
* Private key decrypted data.
* Demonstrated secure communication without sharing the private key.

<br> 

---

<h2>Skills Demonstrated</h2>

* Cryptography Fundamentals
* SHA-256 Hashing
* AES-256 Encryption
* RSA Public Key Infrastructure (PKI)
* Data Integrity Validation
* Secure Key Management 

<br>

---

<h2>Key Takeaways</h2>

* Hashing is valuable for integrity verification and tamper detection.
* SHA-256 is preferred over older algorithms such as MD5 because MD5 has known collision weaknesses.
* Symmetric encryption is efficient for securing stored data but relies heavily on protecting the secret key.
* RSA enables secure communications by separating encryption and decryption responsibilities between public and private keys.
* OpenSSL provides a practical way to implement industry standard cryptographic operations from the command line.

<br>

---

<h2>Reflection</h2>

This lab provided hands-on experience with three core cryptographic concepts: hashing, symmetric encryption, and asymmetric encryption. By generating hashes, encrypting sensitive data with AES-256, and using RSA key pairs, I gained a practical understanding of how confidentiality, integrity, and secure communication are implemented in modern cybersecurity environments. 







   


