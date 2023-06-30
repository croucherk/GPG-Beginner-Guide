# How the OpenPGP Standard Works

* This chapter reviews the fundamental concepts behind OpenPGP before exploring the technical details of one of its most popular software derivatives: GPG
	* The technical details of OpenPGP can be read [here](https://datatracker.ietf.org/doc/html/rfc4880)

## Sections
- [Important Concepts](#important-concepts)
- [OpenPGP Functions](#openpgp-functions)
- [Confidentiality via Encryption](#confidentiality-via-encryption)
- [Non-Repudiation via Signatures](#non-repudiation-via-signatures)

## Important Concepts

* As stated previously, Zimmerman and his team submitted the RFC for OpenPGP to IETF due to its significance in online privacy
	* PGP is the group of software systems originally developed by Zimmerman which OpenPGP is based on
	* The term "OpenPGP" first appears officially in *RFC 2440*[^1], the predecessor of RFC 4880, which succeeded *RFC 1991*[^2]
<br />

* One of the most fundamental concepts in cybersecurity is the *CIA triad*[^3]; each pillar of the triad represents a property of data that ought to be protected
	* *Confidentialtity*[^4]: only authorized individuals should have an appropriate level of visibility of given data
	* *Integrity*[^5]: only authorized individuals should have the ability to modify and/or destroy given data
  	* *Availability*[^6]: given data should be reliably accessed by authorized individuals
* The OpenPGP relates the most to the first two pillars, confidentiality and integrity
	* There are features of OpenPGP that also exist to increase the availability of data
<br />

* The three properties of the CIA triad are not the only properties of data we care about; consider properties like *authenticity*[^7]
	* Authenticity is the assurance that given data actually originated from its supposed source (in most cases, the owner of a private key)
	* OpenPGP supports functions that validate the authenticity of received OpenPGP data
	* *Non-repudiation*[^8] services assure both the authenticity and integrity of data

> *Note*
> Some cybersecurity professionals are probably more familiar with a the second definition of non-repudation: the assurance that both the sender and recipient of data are provided with proof of delivery so that neither can later deny having sent/received it, respectively; the former definition was defined in *FIPS 186-5*[^9], and the latter in *CNSSI 4009-2015*[^10]; both are valid definitions
<br />

## OpenPGP Functions

* According to RFC 4880, OpenPGP supports four major general functions[^11]
	1. Encryption
	2. Digital signatures 
	3. Compression
  	4. Radix-64 conversion
<br />

* *Encryption*[^12] is the process of encoding plaintext information into ciphertext, which obscures the plaintext information to everyone except authorized parties, ensuring confidentiality
	* *Symmetric-key encryption*[^13] algorithms use the same cryptographic key for the encryption of plaintext and the decryption of ciphertext
	* *Asymmetric-key encryption*[^14] algorithms use a pair of a public and secret key to run encryption and decryption functions
	* OpenPGP uses a combination of symmetric and asymmetric functions to create a *hybrid cryptosystem*[^15]
<br />

* *Digital signatures*[^16] are a component in a cryptographic scheme that means to validate 1) the authenticity of the supposed sender, and 2) the integrity of the message
	* Both digital signatures and encryption can be applied to the same message; the encryption process should happen after a file has been digitally sign
	* An important component of the digital signature is the *cryptographic hash function*[^17]; a strong hash function should produce a unique output for every possible input

> **Note**
> Interestingly, RFC 4880 does not require OpenPGP implementations to include encryption **and** digital signature functions[^18]; this is because according to RFC 4880, "there are a number of problems that are solved by a signature-only implementation...it is reasonable for there to be subset implementations that are non-conformant only in that they omit encryption"; for a better understanding of how RFC 4880 specifies what is required/suggested/forbidden from all OpenPGP software implementations, refer to *RFC 2119*[^19], "Key words for use in RFCs to Indicate Requirement Levels"
<br />

* *Compression*[^20] refers to the process of encoding a message in such a way that it is represented using fewer bits than the original message
	* In OpenPGP, it is recommended to induce compression **after** applying a signature but **before** encrypting the same message
	* To ensure the availability of data sent between clients of different OpenPGP software--which might not both support compression--it is recommended to **at least** support decompression
	* Not only does compression mitigate the stress of consuming disk storage, but it can even mitigate common attacks against OpenPGP software[^21]
<br />

* Radix-64 (AKA *base64*)[^22] conversion refers to the process of encoding OpenPGP's native binary octet representation of messages/files/keys into ASCII characters
	* Some channels (like those of older mail servers) are not considered safe for transporting raw binary content, as they may result in unwanted data conversion; these channels can much more reliably transport textual content
	* OpenPGP's implementation of radix-64 conversion results in two parts: the encoded message, and a *checksum*[^23] to detect integrity failures
	* The radix-64 encoding mechanism was first described in *RFC 989*[^24], and the common name for the encoding scheme and its conversion table were defined in *RFC 4648*[^25] 
<br />

## Introduction to Alice, Bob, and Charlie

* For this and subsequent sections, we will explore the complicated relationship of Alice, Bob, and Charlie
	* All three are cybersecurity and cryptography professionals
	* Alice is a well respected penetration tester at a top cybersecurity firm in the UK, nice!!
	* Bob is the CISO of a Fortune 500 company in the USA, wow!!
	* Charlie used to be a fun guy, but has now fallen out with Alice and Bob after the two of them found out about his side gig: developing and selling ransomware on the Dark Web, for shame!!

* Alice and Bob continue to maintain their friendship and professional partnership online via email
	* Recently they discovered reason to believe that Charlie has been eavesdropping on their communications
	* Their messages often contain privileged information; if Charlie were to sell this information or use it to conduct an attack against Alice's or Bob's company, it could have a devastating impact on people's lives
	* After reporting their concerns to the relevant authorities, Alice and Bob decided to start using an email client supporting *end-to-end encryption* (E2EE)[^26] and adhering to the OpenPGP standard

* We will use Alice, Bob, and Charlie to illustrate how OpenPGP protects its users from attacks on confidentiality and authenticity
<br />

## Confidentiality via Encryption

* Alice and Bob want to send messages to each other with the guarantee that Charlie can not read their messages
	* This is an example of confidentiality, and it is achieved by encryption
<br />

* OpenPGP uses the following steps to encrypt a given message:
	1. The sender (Alice) creates a plaintext message
	2. Alice's sending OpenPGP software generates a random session key; this session key will only be used for this message only
	3. The sending OpenPGP software encrypts the message using the session key
	4. The sending OpenPGP software encrypts the session key with the recipient (Bob's) public key
	5. The encrypted session key is appended to the encrypted message
	6. The encrypted message and encrypted session key are sent to the recipient (Bob)
	7. Bob's receiving OpenPGP software decrypts the encrypted session key with Bob's private key
	8. Bob's receiving OpenPGP software decrypts the encrypted message with the session key

> *Note*
> The plaintext message is usually compressed prior to encryption, and when it is decrypted it is subsequently decompressed

![Confidentiality via Encryption Diagram](/content/img/encryption-diagram.png)
<br />

* There are two objects Charlie can attempt to crack: the encrypted file and the encrypted session key
	1. Because the message was encrypted with the plaintext version of the session key, it would be infeasibly difficult for Charlie to crack the encrypted file without access to the decrypted session key
	2. As long as Bob keeps his private key absolutely secret, then it would be infeasibly difficult for Charlie to crack the decrypted session key
<br />

## Non-Repudiation via Signatures

* Alice and Bob want to send messages that they know have only been modified by themselves
	* This is an example of integrity, and it is achieved by digital signatures
* They also want to receive messages that they can validate originated from each other
	* This is an example of authenticity, and it is also achieved by digital signatures
* With both of these assurances, Alice and Bob will have achieved non-repudiation
<br />

* OpenPGP uses the following steps to digitally sign a message
	1. The sender (Alice) creates a plaintext message
	2. Alice's sending OpenPGP software generates a hash code of the plaintext message
	3. The sending OpenPGP software encrypts the message hash with Alice's private key, creating a signature
	4. The sending OpenPGP software attaches the signature to the message
	5. The signature and message are sent to the recipient (Bob)
	6. Bob's receiving OpenPGP software creates a copy of the signature and decrypts it with Bob's public key, leaving only the hash
	7. Bob's receiving OpenPGP software creates a new hash code of the received message using the same hash algorithm used by Alice previously
	8. If both the hash from the signature and the newly generated hash of the message match, then the message is deemed authentic

> *Note*
> If the message were encrypted **and** signed, then the encryption would happen immediately after step 4, and decryption would happen immediately before step 6

![Authenticity via Signatures Diagram](/content/img/signature-diagram.png)
<br />

* Assuming Charlie has access to an unencrypted copy of the message and signature, then Charlie can attempt two attacks: modifying the original message and/or replacing the signature
    1. If Charlie were to modify the original message, then the hash of the modified file would be different to the original hash (as long as Alice and Charlie are using a *collision-resistant*[^27] hash algorithm), and the message would be rejected
    2. Charlie could attempt replacing the signature with one containing a hash matching his corrupted copy of the message, but as long as Alice keeps her private key absolutely secret, then Charlie can not duplicate Alice's signature, and Bob's receiving OpenPGP software would fail to decrypt the signature with Alice's public key, and the message would be rejected
<br />

[^1]:https://datatracker.ietf.org/doc/html/rfc2440
[^2]:https://datatracker.ietf.org/doc/html/rfc1991
[^3]:https://csrc.nist.gov/glossary/term/cia
[^4]:https://csrc.nist.gov/glossary/term/confidentiality
[^5]:https://csrc.nist.gov/glossary/term/integrity
[^6]:https://csrc.nist.gov/glossary/term/availability
[^7]:https://csrc.nist.gov/glossary/term/authenticity
[^8]:https://csrc.nist.gov/glossary/term/non_repudiation
[^9]:https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-5.pdf#%5B%7B%22num%22%3A26%2C%22gen%22%3A0%7D%2C%7B%22name%22%3A%22XYZ%22%7D%2C70%2C698%2C0%5D
[^10]:https://rmf.org/wp-content/uploads/2017/10/CNSSI-4009.pdf
[^11]:https://datatracker.ietf.org/doc/html/rfc4880#section-2
[^12]:https://csrc.nist.gov/glossary/term/encryption
[^13]:https://csrc.nist.gov/glossary/term/symmetric_encryption_algorithm
[^14]:https://csrc.nist.gov/glossary/term/asymmetric_cryptography
[^15]:https://en.wikipedia.org/wiki/Hybrid_cryptosystem
[^16]:https://csrc.nist.gov/glossary/term/digital_signature
[^17]:https://csrc.nist.gov/glossary/term/cryptographic_hash_function
[^18]:https://datatracker.ietf.org/doc/html/rfc4880#section-2.5
[^19]:https://datatracker.ietf.org/doc/html/rfc2119
[^20]:https://en.wikipedia.org/wiki/Data_compression
[^21]:https://www.schneier.com/academic/archives/2002/01/implementation_of_ch.html
[^22]:https://en.wikipedia.org/wiki/Base64
[^23]:https://csrc.nist.gov/glossary/term/checksum
[^24]:https://datatracker.ietf.org/doc/html/rfc989
[^25]:https://datatracker.ietf.org/doc/html/rfc4648
[^26]:https://csrc.nist.gov/glossary/term/end_to_end_encryption
[^27]:https://csrc.nist.gov/glossary/term/collision_resistance
