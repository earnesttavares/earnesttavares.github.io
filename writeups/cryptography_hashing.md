---
title: Cryptography & Hashing
parent: Writeups
nav_order: 3
---

<h1 style="color:#33aaff;">Cryptography & Hashing with OpenSSL</h1> 

<br>

<h2>Objective</h2>


**Cryptography is a foundational component of cybersecurity** that protects the confidentiality, integrity, and authenticity of information. It powers secure communication protocols like HTTPS, protects passwords, enables encrypted messaging, and forms the backbone of digital identity verification. This lab introduces core cryptographic concepts using OpenSSL, specifically exploring hashing, symmetric encryption, and asymmetric encryption. The objective was to demonstrate how these mechanisms validate data integrity and enforce strong access controls.  

<br>

---

<h2>Lab Environment</h2>

* **Operating System**: Ubuntu Linux
* **Tools**: OpenSSL, Nano text editor
* **Cryptographic Frameworks**: SHA-256 hashing, AES-256 symmetric encryption, RSA asymmetric encryption

<br> 

---

<h2>Step-by-Step Analysis</h2>

<br>

### Part A: SHA-256 Hashing

<details markdown="block"><summary>View Commands & Terminal Output</summary>

<br>

🔸 Created a Test File: 

&emsp;**Command Execute:** 
``` bash
echo “This is my first Cybersecurity Lab” > file1.txt 
```

<br>

🔸 Generated a SHA-256 Hash Digest:

&emsp;**Command Execute:** 
``` bash
openssl sha256 file1.txt
```

&emsp;**Terminal Output:**
```text
SHA2-256(file1.txt)= e9853110aca471a30c1610a2527f5d6551b168f6a2b88dfa6a891334a2d1fc21
```

&emsp;**Observation:** OpenSSL generated a unique, fixed-length 256-bit hexadecimal string. This cryptographic digest serves as the file's unique digital fingerprint, proving that any future alteration to the underlying data block will disrupt the output. 

<br>

🔸 Verified Hash Consistency Across Duplication:
   
&emsp;*Created a duplicate file:* <br>

&emsp;**Command Execute:** 
``` bash
cp file1.txt file2.txt
```

<br>

&emsp;*Hashed the copied file:* <br>

&emsp;**Command Execute:** 
``` bash
openssl sha256 file2.txt
```
&emsp;**Terminal Output:**
```text
SHA2-256(file2.txt)= e9853110aca471a30c1610a2527f5d6551b168f6a2b88dfa6a891334a2d1fc21
```

&emsp;**Observation:** The resulting hash output for `file2.txt` matched the digest of `file1.txt`. This confirms that mathematical hash functions operate strictly on raw data contents rather than file naming conventions or filesystem metadata attributes.

<br>

🔸 Demonstrated Hash Sensitivity (The Avalanche Effect):

&emsp;*Modified the original plaintext input. Added a (.) at the end of the sentence in file1.txt:* <br>

&emsp;**Command Execute:** 
``` bash
nano file1.txt
```

&emsp;*Re-generated the hash:* <br>

&emsp;**Command Execute:** 
``` bash
openssl sha256 file1.txt
```

&emsp;**Terminal Output:**
```text
SHA2-256(file1.txt)= 704a9d8469aa553918cefc2c96b5c3eeac6aa49980cc9490870c80d64336787d
```

&emsp;**Observation:** A single character modification generated an entirely unique hash, demonstrating the avalanche effect and hash sensitivity. This ensures robust data integrity because any minor manipulation to the data content radically transforms the cryptographic fingerprint, meaning any unauthorized tampering cannot be masked. 

</details>

<br>

--- 

### Part B: AES-256 Encryption 

<details markdown="block"><summary>View Commands & Terminal Output</summary>

<br>

🔸 Created a Sensitive File:

&emsp;**Command Execute:** 
``` bash
echo “This is top secret information.” > secret.txt 
```

<br>

🔸 Encrypted the Plaintext File Using AES-256-CBC:

&emsp;**Command Execute:** 
``` bash
openssl enc -aes-256-cbc -pbkdf2 -salt \ -in secret.txt \ -out secret.enc
```
&emsp;*After entering the above command, the terminal prompts the user to enter a password. This password is used as the encryption key.*

<br>

&emsp;*Syntax Analysis:* <br>
| Flag | Purpose |
| :--- | :--- | 
| openssl enc | Tells OpenSSL to use its encryption module | 
| -aes-256-cbc | Specifies the encryption algorithm | 
| -pbkdf2 | Uses a secure password-based key derivation function | 
| -salt | Adds randomness making it harder to brute-force | 
| -in secret.txt | The input file you want to encrypt | 
| -out secret.enc | The output file that will contain the encrypted version | 

<br>

🔸 Decrypted the File:

&emsp;**Command Execute:** 
``` bash
openssl enc -d -aes-256-cbc -pbkdf2 \ -in secret.enc \ -out decrypted.txt
```

<br>

&emsp;*View Encrypted Ouput:* <br>
&emsp;**Command Execute:** 
``` bash
cat decrypted.txt
```

&emsp;*Result:* 

&emsp;**Terminal Output:** 
``` text
"This is top secret information."
```

&emsp;**Observation:** AES uses a shared secret key for both encryption and decryption. Successful recovery of the plaintext demonstrated how confidentiality depends on protecting the encryption key. 

</details>

<br>

---

### Part C: RSA Encryption 

<details markdown="block"><summary>View Commands & Terminal Output</summary>

<br>

🔸 Generated an RSA Key Pair:

&emsp;*Generated the Private Key:* 

&emsp;**Command Execute:** 
``` bash
openssl genpkey -algorithm RSA \ -out private.pem \ -pkeyopt rsa_keygen_bits:2048
```

<br>

&emsp;*Syntax Analysis:* <br>
| Flag | Purpose | 
| :--- | :--- | 
| openssl genpkey | Uses OpenSSL’s key generation tool | 
| -algorithm RSA | Specifies the RSA algorithm | 
| -out private.pem | Saves the generated private key to the file named private.pem | 
| -pkeyopt rsa_keygen_bits:2048 | Sets the key size to 2048 bits, the common secure default | 

<br>

&emsp;**Terminal Output:**
```text
....+..+...+.+.....+...+.+......+.....+....+..+....+...+........+..........+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.+.......+......+...........+.+..+............+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*...+..+.+..+.......+...........+...+.......+..+......+....+..+.+............+...+.....+.............+...............+......+.....+.+..............+...+...+......................+........+...+....+..+.+.........+........+.+......+..............+.......+...+....................+.........+......+.+.....+.+.....+...+..............................+..........+..+...+....+..+...............+.+..+..................+....+.....+....+..+...+..........+...........+.........+.......+........+....+.....+......+.+.....+...+.+.........+...+.....+....+.....+....+..+..........+....................+...+..........+..+..................+.+.....+.+...+.....+....+...........+....+..+....+.........+..+...+.+..+.........+....+.........+.....+.+.........+..+...+.......+......+.....+.......+..................+...+.................+.......+......+..+....+...+...+.....+...+.......+...+............+........+......+..........+.....+.+.....+...+.........+..................+.......+...+........+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
.+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*..+...+...+....+...+..+.+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.....+.+.........+.....+...............+......+...+.........+.+..+............+...+....+...........+...+.......+...............+..+.+.....+....+..+.+........+....+...+............+..+...+.......+...........+.............+..+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
```

```text
RSA key generation completed successfully.
```

<br>

&emsp;*Then Extracted the Public Key:* 

&emsp;**Command Execute:**
``` bash
openssl rsa -pubout \ -in private.pem \ -out public.pem
```

<br>

&emsp;*Syntax Analysis:* <br>
| Flag | Purpose | 
| :--- | :--- | 
| openssl rsa | Uses OpenSSL’s RSA key management tool | 
| -pubout | Tells OpenSSL to output the public key from the private key | 
| -in private.pem | Specifies the input file containing the RSA private key | 
| -out public.pem | Specifies the output file to save the extracted public key | 

<br>

&emsp;**Terminal Output:**
```text
writing RSA key
```

<br>

🔸 Encrypted a Message Using the Public Key:

&emsp;**Command Execute:**
``` bash
echo “RSA encryption in action!” > message.txt 
```

<br>

🔸 Encrypted with Public Key:

&emsp;**Command Execute:**
``` bash
openssl pkeyutl -encrypt \ -pubin \ -inkey public.pem \ -in message.txt \ -out encrypted.bin
```

<br>

&emsp;*Syntax Analysis:* <br>
| Flag | Purpose | 
| :--- | :--- |
| openssl pkeyutl | Uses OpenSSL’s public/private key utility tool | 
| -pubin | Tells OpenSSL that the input key (-inkey) is a public key | 
| -inkey public.pem | Points to the public key file used for encryption | 
| -in message.txt | Specifies the plaintext file to encrypt | 
| -out encrypted.bin | Specifies the output file for the encrypted data | 

<br>

🔸 Decrypted the Message with the Private Key:

&emsp;**Command Execute:**
``` bash
openssl pkeyutl -decrypt -inkey private.pem -in encrypted.bin -out decrypted.txt 
```

<br>

&emsp;*Displayed the Message:* 

&emsp;**Command Execute:**
``` bash
cat decrypted.txt
```

&emsp;*Result:* 

&emsp;**Terminal Output:**
``` text
RSA encryption in action!
```

&emsp;**Observation:** The message could only be decrypted using the corresponding private key, demonstrating how public/private key pairs provide secure trust-based communication. 

</details>

<br>

---

<h2>Key Findings & Artifacts</h2>

<br>

<u>Hashing</u> <br> 

* SHA-256 produces a unique digest for file content.
* Identical files generated identical hashes.
* A one-character modification produced a completely different hash.
* Demonstrated how SHA-256 can be used to verify file integrity and detect unauthorized modifications.
   
&emsp;**Artifact:** [Image]

<br>

<u>AES Encryption</u> <br> 

* Encrypted plaintext into ciphertext.
* Successfully decrypted ciphertext using the correct password, demonstrating confidentiality through symmetric encryption. 
* Confidentiality depends on proper key management.
  
&emsp;**Artifact:** [Image]

<br>

<u>RSA Encryption</u> <br>

* Generated a 2048-bit public/private key pair.
* Used the public key to encrypt data. 
* Used the corresponding private key to decrypt and recover the original plaintext. 
* Demonstrated secure communication without sharing the private key.

&emsp;**Artifact:** [Image]

<br> 

---

<h2>Security Impact</h2>

Organizations rely on cryptographic controls to protect sensitive data and maintain trust in their systems. During this lab, SHA-256 hashing was used to validate data integrity by detecting file modifications. AES-256 encryption demonstrated how confidential information can be protected from unauthorized access, while RSA encryption illustrated how secure communications can occur through public and private key pairs. 

The concepts practiced in this exercise are foundational to technologies such as HTTPS, VPNs, digital certificates, secure email, software signing, and enterprise data protection solutions. 

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

<h2>Reflection</h2>

This lab demonstrated that effective cybersecurity extends beyond implementing technical controls and requires understanding the security principles they support. Hashing, symmetric encryption, and asymmetric encryption each serve distinct purposes, but together they help protect the confidentiality, integrity, and trustworthiness of information. Through hands-on use of OpenSSL, I developed a deeper understanding of how these cryptographic mechanisms work behind the technologies that organizations rely on daily to secure communications, validate software, and protect sensitive data. 
