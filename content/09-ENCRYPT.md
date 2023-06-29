# Encryption and Decryption

* GPG is a file and message encrypting software; now that we have covered setting up our own keypair and importing+signing other's keys, we can begin encrypting files for communication

## Sections
- [Encrypting a File for a Single Recipient](#encrypting-a-file-for-a-single-recipient)
- [Encrypting a File for Multiple Recipients](#encrypting-a-file-for-multiple-recipients)

## Encrypting a File for a Single Recipient

* To encrypt a file/message for an individual, we use the `gpg --encrypt` command and specify the individual with the `--recipient` command
	* In this example, we used the `--armor` to instruct GPG to save the output in ASCII format
	* The output of this command will be a second file with the same name as the plaintext file and an .asc extension

```console
foo@bar:~$ cat encrypt-this.txt
Super secret message!!

foo@bar:~$ gpg --armor --recipient Alice --encrypt encrypt-this.txt

foo@bar:~$ ls | grep encrypt-this.txt
encrypt-this.txt
encrypt-this.txt.asc

foo@bar:~$ cat encrypt-this.txt.asc
-----BEGIN PGP MESSAGE-----

hF4DR2b2udXyHrYSAQdAOiQJ9PqH0zYqxBm+rRV4Z9ALwmChnaSVSRVZpxAxKlMw
Po+hqAXPFLx0wRgRe9j2VYZl29R3YG8DPy2gUGIL0nAa61k2rZ97E3AL/5QfmHzi
0mIBT1tvEItVDCcy+359sXcsLOzjX04IoC6cAhb0Y/+Qj0+XPPDKIlA8rsWIXvhh
zqAWK/CV9Xe8lyTlB9eIx5iYDxjN28Rjunwj69MMnWKjdj+oAgiKxUIwWYvKXD27
uLjkcw==
=xJXJ
-----END PGP MESSAGE-----
```
<br />

* We can try decrypting this message with the `gpg --decrypt` command
	* Because we do not have Alice's private key, we can not decrypt the message

```console
foo@bar:~$ gpg --decrypt encrypt-this.txt.asc   
gpg: encrypted with 255-bit ECDH key, ID 4766F6B9D5F21EB6, created 2019-01-22
      "Alice Lovelace <alice@openpgp.example>"
gpg: decryption failed: No secret key
```
<br />

## Encrypting a File for Multiple Recipients

* There may come a point where we have to send an encrypted file/message to multiple recipients
	* We can't just encrypt the file once with a single public key, because then how would all recipients be able to read it (only the recipient with the private key associated with the encrypting public key could decrypt it)?
	* GPG works around this by encrypting the plaintext file multiple times with multiple public keys, then sending all the ciphertexts as a single file (each recipient can only decrypt the section belonging to themselves)

* Let's `cp` our original plaintext, then `encrypt` the file with Alice and **ourselves** as the recipients
	* We can chain the `--recipient` flag multiple times in the same command

```console
foo@bar:~$ cp encrypt-this.txt encrypt-this-2.txt

foo@bar:~$ cat encrypt-this-2.txt                
Super secret message!!

foo@bar:~$ gpg --armor --recipient Alice --recipient Foo --encrypt encrypt-this-2.txt

foo@bar:~$ cat encrypt-this-2.txt.asc 
-----BEGIN PGP MESSAGE-----

hF4DR2b2udXyHrYSAQdArYhGO5AQY2OjMVgMwVBBcGpQdMHAME9JY7PhX2L7tBAw
3vImU5UFGnte5wFCyhhz/FhsaIyvc06pVkYMI7hwn5WVjvbWeUrZh5TFqWru0E9S
hQIMAwsZaH1XBQlWARAAl3kV4fmNs4c2/1PvY0202oFzBt9PhxBuwPa+iaZtXWuS
JR9EG/79C08TnsvMAOsRwBo7CW+pbxq9q7BZKHcv/mlviEs6t6JLSrzAdxLW8wLC
3y7hvIpSlEsSaVMXU4ARH+0D9KWFs2MHLcDPAapZtSz72Xa+Yjwn/u0Y6zcihmme
oTmtHF7Z6lPCU8L6615O+i/OJ3+1FHsRcIPV2j+Cz9/vva3nikAG/fMQdSRr+Q3Z
+2lCS24hsB/1D0fH5ccppvMQCHaN7Pn7kNOyLBUEuBv0b/pnmRZb5bfthtIcH9w1
dzNQtJyInqhSemKrCmc5gvSp5j2vXp9zvVCAHh0T0j+cJFy81kHPGcsVOubYMNyL
AaQtjdhnd91S5goKFFZYHSJ5Jd8Fp8RYxn40DCN5iNN5xrtIH4LkxvjmGCSFe0+h
Daebu0cxXEd8jLXs5K9ICSfoRm0pqLfBMRnfyhkMNB0khrqYx/byC5qOnf+VFArm
tPmAZ0lmo89kLpLCEWvM3kxprtnRe7hTli25Rw+IcGrFqRbnhgsFbSCVqObcawyn
DQhFvkHHgMKkJcZgyMsbCvikYAl5BbE8NBydemrivI+pWAVEOdbsxWRiSESUfEdV
m/QUel8WUurvFqLlKoQoTNu/742BuFGJKppjH24v/R5R9PxZAHrwuOJy9bvo7GHS
ZAFCMYJ+2efaOMVVM1IFhI4upVlcpYc4Uvf3rFzpdPLMlzcKeDyIFEuqZIOtlrwX
2k5l2a37xrO/7BVz5V00Xwqc7lLL4SGnFAABvauJ1xA0JGZYKn34HYUadd+WK0Ls
0oYC5L4=
=N957
-----END PGP MESSAGE-----

foo@bar:~$ gpg --decrypt encrypt-this-2.txt.asc                                             
gpg: encrypted with 255-bit ECDH key, ID 4766F6B9D5F21EB6, created 2019-01-22
      "Alice Lovelace <alice@openpgp.example>"
gpg: encrypted with 4096-bit RSA key, ID 0B19687D57050956, created 2023-01-01
      "Foo Bar (Good idea to add a comment here) <foobar@baz.com>"
Super secret message!!
```
<br />

* From the output above, we can see our decrypted message, "Super secret message!!"
	* Notice how GPG detected that this file was encrypted with two keys, but only decrypted the message encrypted with Foo Bar's public key
