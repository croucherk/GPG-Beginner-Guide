# Signing Files

* While encryption offers protection against confidentiality attacks, it does not guarantee authenticity (or non-repudiation) of any message
	* Recall that messages are encrypted by public keys, which are definitionally available to the public
	* Anyone can use someone's public key to encrypt a message, so how do we use GPG to 1) ensure that only its intended recipient can read them and 2) prove that a file was encrypted by the legitimate owner of that public key?
* Digital signatures certify that the owner of a private key signed this file at a given time

## Sections
- [Signed Files](#signed-files)
- [Verifying a Signed File](#verifying-a-signed-file)
- [Clearsigned Files](#clearsigned-files)
- [Detached Signatures](#detached-signatures)
- [Encrypting and Signing Files](#encrypting-and-signing-files)

## Signed Files

* A file can be digitally signed by running the `gpg --sign [file]` command

```console
foo@bar:~$ cat sign-this.txt
Sign this message with Foo Bar's private key

foo@bar:~$ gpg --sign sign-this.txt

foo@bar:~$ ls | grep sign-this
sign-this.txt
sign-this.txt.gpg
```
<br />

* GPG signed the `sign-this.txt` file with Foo Bar's private key and saved the signed file in binary GPG format
	* We may want to save the signed file in human-readable format, so let's reattempt signing the file with the added `--armor` flag

```console
foo@bar:~$ gpg --armor --sign sign-this.txt

foo@bar:~$ cat sign-this.txt.asc     
-----BEGIN PGP MESSAGE-----

[...]
a4+YC4FL73SQJF6KvqFoQToFx1pgUHkXCmWFbKRoRObeQH7D0p4qkdRm5HaZOdj0
zN6aOPrykOrGeHFw+mXGNLbCQZWwUWTxb1tLIgCZ
=3FDS
-----END PGP MESSAGE-----

foo@bar:~$ ls | grep sign-this
sign-this.txt
sign-this.txt.asc
sign-this.txt.gpg
```
<br />

## Verifying a Signed File

* We can verify the signature of `sign-this.txt.gpg` with the `gpg --verify [file]` command
	* This command will only output which key was used to sign the file, it will not extract the file's plaintext contents

```console
foo@bar:~$ gpg --verify sign-this.txt.gpg
gpg: Signature made Sun 01 Jan 2023 12:00:00 PM EDT
gpg:                using RSA key 8681B558A2B654933E69834CC8662846CCEA9E27
gpg: Good signature from "Foo Bar (Good idea to add a comment here) <foobar@baz.com>" [ultimate]
gpg:                 aka "[jpeg image of size 5217]" [ultimate]
```
<br />

* To verify a file's signature **and** extract the file's contents, use the `gpg --decrypt [file]` command
> **Note**
> Just because we use the `--decrypt` option here, it does not mean that the message itself is encrypted; in GPG, decryption is a 2 step process: 1) verify a signature if it exists on a file, and 2) decrypt the file contents if we have the correct private key

```console
foo@bar:~$ gpg --output sign-this-output --decrypt sign-this.txt.gpg
gpg: Signature made Sun 01 Jan 2023 12:00:00 PM EDT
gpg:                using RSA key 8681B558A2B654933E69834CC8662846CCEA9E27
gpg: Good signature from "Foo Bar (Good idea to add a comment here) <foobar@baz.com>" [ultimate]
gpg:                 aka "[jpeg image of size 5217]" [ultimate]

foo@bar:~$ cat sign-this-output 
Sign this message with Foo Bar's private key
```
<br />

* Let's attempt the same signature verifying process on `sign-this.txt.asc`

```console
foo@bar:~$ gpg --verify sign-this.txt.asc                           
gpg: Signature made Sun 01 Jan 2023 12:00:00 PM EDT
gpg:                using RSA key 8681B558A2B654933E69834CC8662846CCEA9E27
gpg: Good signature from "Foo Bar (Good idea to add a comment here) <foobar@baz.com>" [ultimate]
gpg:                 aka "[jpeg image of size 5217]" [ultimate]
gpg: WARNING: not a detached signature; file 'sign-this.txt' was NOT verified!

foo@bar:~$ gpg --output sign-this-output-2 --decrypt sign-this.txt.asc
gpg: Signature made Sun 01 Jan 2023 12:00:00 PM EDT
gpg:                using RSA key 8681B558A2B654933E69834CC8662846CCEA9E27
gpg: Good signature from "Foo Bar (Good idea to add a comment here) <foobar@baz.com>" [ultimate]
gpg:                 aka "[jpeg image of size 5217]" [ultimate]

cat sign-this-output-2 
Sign this message with Foo Bar's private key
```

> **Note** 
> The process was almost identical, but when we ran `gpg --verify sign-this.txt.asc` we received a warning message we did not see before: `not a detached signature; file 'sign-this.txt' was NOT verified!`; GPG is detecting `sign-this.txt` in our current working directory and is assuming we want to use `sign-this.txt.asc` to verify that the contents of `sign-this.txt` have not been changed, which is not possible at the moment; try renaming `sign-this.txt` and running the same `gpg --verify sign-this.txt.asc` command and the warning should disappear
<br />

## Clearsigned Files

* The `gpg --sign` command will compress the original file when signing it, but this is not always desirable
* To attach a digital signature to a file without compressing its contents, use the `gpg --clearsign [file]` command

```console
foo@bar:~$ cp sign-this.txt clearsign-this.txt
                                                                                                                                         
foo@bar:~$ gpg --clearsign clearsign-this.txt
                                                                                                                                         
foo@bar:~$ cat clearsign-this.txt.asc 
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA512

Sign this message with Foo Bar's private key
-----BEGIN PGP SIGNATURE-----
[...]
tQdvYyY1AgWxy/A4K2QuY4g98U8h57rQlRjWLTAug12r9xlUMocf4ZDdKlTsD/Fv
dGsBF7jjT/D0IoVdDLbD8uWCRwngcgFT+5LYToeOJ1kZfpjHkpQ=
=t9ur
-----END PGP SIGNATURE-----
```

* As you can see from the output, the file is in ASCII format, not binary format
<br />

## Detached Signatures

* In some cases it may not be advantageous to send a file as a signed file or as a clearsigned file
	* In a signed file, the package contains a compressed version of the original and a digital signature; both have to be recovered manually by the recipient
	* In a clearsigned file, the original file can't be entirely recovered without removing the PGP signature and headers
* Detached signatures are independent signature files "detached" from the file being signed
	* This is especially useful in a case where a user wants to send a signed file to a recipient that they expect the recipient to edit; the recipient can use the signature to validate the file, then make edits to the file without any intermediary steps
	* Run the `gpg --detach-sig [file]` command to generate a detached signature in binary format

```console
foo@bar:~$ cp sign-this.txt detachsign-this.txt

foo@bar:~$ gpg --detach-sig detachsign-this.txt

foo@bar:~$ ls | grep detachsign-this         
detachsign-this.txt
detachsign-this.txt.sig
```
<br />

* To verify a detached signature, you have to pass the signature and original file to the `gpg --verify [signature] [file]` command

```console
gpg --verify detachsign-this.txt.sig detachsign-this.txt
gpg: Signature made Sun 01 Jan 2023 12:00:00 PM EDT
gpg:                using RSA key 8681B558A2B654933E69834CC8662846CCEA9E27
gpg: Good signature from "Foo Bar (Good idea to add a comment here) <foobar@baz.com>" [ultimate]
gpg:                 aka "[jpeg image of size 5217]" [ultimate]
```
<br />

* The same process can be done with detached signatures in ASCII format

```console
foo@bar:~$ gpg --armor --detach-sig detachsign-this.txt

foo@bar:~$ cat detachsign-this.txt.asc
-----BEGIN PGP SIGNATURE-----

[...]
4/A7Y+VnlOaQw8fNs5GSvTpnTPiO4p59VUfmAnBoGH6ezpWOkl5WxUiFyditmhGy
hVfca1UtNfLruBgMCzRMYFMtWHe9XE37DeOCONOewZqoXaDurWQ=
=Da4t
-----END PGP SIGNATURE-----

foo@bar:~$ gpg --verify detachsign-this.txt.asc detachsign-this.txt
gpg: Signature made Sun 01 Jan 2023 12:00:00 PM EDT
gpg:                using RSA key 8681B558A2B654933E69834CC8662846CCEA9E27
gpg: Good signature from "Foo Bar (Good idea to add a comment here) <foobar@baz.com>" [ultimate]
gpg:                 aka "[jpeg image of size 5217]" [ultimate]
```
<br />

## Encrypting and Signing Files

* None of the files we signed so far have been encrypted; if these were intercepted in transit, their contents could be read
* Let's create an encrypted and signed file first using Alice's key, then attempt to read the file contents

```console
foo@bar:~$ cat encrypt-this.txt
Super secret message!!

foo@bar:~$ gpg --armor --recipient Alice --encrypt --sign encrypt-this.txt

foo@bar:~$ gpg --decrypt encrypt-this.txt.asc                    
gpg: encrypted with 255-bit ECDH key, ID 4766F6B9D5F21EB6, created 2019-01-22
      "Alice Lovelace <alice@openpgp.example>"
gpg: decryption failed: No secret key
```
> **Note** 
> Note how we ran the encryption and signing process on the same line instead of two separate commands; if we ran the commands separately and sent them to our recipient, then the `gpg --decrypt [file]` command would **only** decrypt the encrypted contents **or** verify the signature, depending on which operation we ran first

* As expected, we are unable to view the file's signature or its decrypted contents
<br />

* Let's encrypt and sign the same file again, this time specifying that we are the recipients

```console
foo@bar:~$ gpg --armor --recipient Foo --encrypt --sign encrypt-this.txt

foo@bar:~$ gpg --decrypt encrypt-this.txt.asc
gpg: encrypted with 4096-bit RSA key, ID 0B19687D57050956, created 2023-01-01
      "Foo Bar (Good idea to add a comment here) <foobar@baz.com>"
Super secret message!!
gpg: Signature made Sun 01 Jan 2023 12:00:00 PM EDT
gpg:                using RSA key 8681B558A2B654933E69834CC8662846CCEA9E27
gpg: Good signature from "Foo Bar (Good idea to add a comment here) <foobar@baz.com>" [ultimate]
gpg:                 aka "[jpeg image of size 5217]" [ultimate]
```
<br />

* We have successfully encrypted and signed a file addressed to ourselves, then decrypted the file to reveal its signature and decrypted contents
