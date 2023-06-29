# Generating a Keypair

* The first step in using GPG is almost always to generate an asymmetric keypair
	* Let's review the steps to generate your keypair, as well as some actions you should/may have to take after

## Sections
- [Generating an Asymmetric Keypair](#generating-an-asymmetric-keypair)
- [Generating a Revocation Certificate](#generating-a-revocation-certificate)
- [Exporting Your Keys](#exporting-your-keys)
- [Deleting Your Keys](#deleting-your-keys)

## Generating an Asymmetric Keypair

* In this example, we will be creating an asymmetric keypair with the *Rivest–Shamir–Adleman* (RSA) public key cryptosystem[^1]
* Run the  command `gpg –-full-generate-key` to begin the GPG key generation interactive process
	* You will first be asked to specify your encryption algorithm, key length, a passphrase, and optionally an expiration date for your keys
	* Choose the *RSA and RSA (default)* option for your public/private keys by pressing `1` or <kbd>Enter</kbd>

```console
foo@bar:~$ gpg –-full-generate-key
gpg (GnuPG) 2.2.40; Copyright (C) 2022 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
  (14) Existing key from card
Your selection? 
```
<br />

* Choose a key length of `4096` bits (Since 2020[^2][^3], NIST’s minimum key length recommendation for RSA algorithms is 2048 bits)
	* Remember that the higher the length of a key, the more difficult it becomes to crack

```console
...
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072) 4096
Requested keysize is 4096 bits
```
<br />

* You may also choose an expiration date of your keys
	* Although in this example we choose a key that does not expire, this is not always considered best practice
	* Since 2020[^4], NIST's *Originator Usage Period* (OTP)[^5] recommendation for private signature keys has been 1-3 years

```console
...
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 0
Key does not expire at all
Is this correct? (y/N) y
```
<br />

* GPG will ask you for your real name and email address to craft a user ID (UID) to uniquely identify your key
	* You can choose to add a comment to your key's UID for added distinctness

```console
...
Real name: Foo Bar
Email address: foobar@baz.com
Comment: Good idea to add a comment here
You selected this USER-ID:
    "Foo Bar (Good idea to add a comment here) <foobar@baz.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
```
<br />

* After entering your name, email, and comment, a new window will appear prompting you for a key passphrase
	* Key passphrases are optional but **highly** encouraged since it will mitigate the risk of your private key being used by an individual if they gain unauthorized access to your key
	* To skip the key passphrase stage, just simply click *OK* and accept the warning message that appears
* After entering/not entering a passphrase, the key generation process will complete

```console
...
gpg: revocation certificate stored as '/home/foo/.gnupg/openpgp-revocs.d/8681B558A2B654933E69834CC8662846CCEA9E27.rev'
public and secret key created and signed.

pub   rsa4096 2023-01-01 [SC]
      8681B558A2B654933E69834CC8662846CCEA9E27
uid                      Foo Bar (Good idea to add a comment here) <foobar@baz.com>
sub   rsa4096 2023-01-01 [E]
```
<br />

* To verify the generation process completed successfully, run the following command to list public and private keys, respectively:
	* The first command, `gpg --list-keys` lists all public keys managed by the GPG keyring (the `gpg --list-public-keys` command achieves the same function)
	* The second command, `gpg --list-secret-keys` lists all private keys managed by the GPG keyring

```console
foo@bar:~$ gpg --list-keys                                           
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
/home/foo/.gnupg/pubring.kbx
-----------------------------
pub   rsa4096 2023-01-01 [SC]
      8681B558A2B654933E69834CC8662846CCEA9E27
uid           [ultimate] Foo Bar (Good idea to add a comment here) <foobar@baz.com>
sub   rsa4096 2023-01-01 [E]

foo@bar:~$ gpg --list-secret-keys
/home/foo/.gnupg/pubring.kbx
-----------------------------
sec   rsa4096 2023-01-01 [SC] 
      8681B558A2B654933E69834CC8662846CCEA9E27
uid           [ultimate] Foo Bar (Good idea to add a comment here) <foobar@baz.com>
ssb   rsa4096 2023-01-01 [E]
```

* According to the output, your keys are now a part of your virtual GPG keyring
<br />

## Generating a Revocation Certificate

* A revocation certificate can be used in the case that you forget your private key passphrase, your private key is compromised, or for any other reason justifying a complete revocation of your keypair
	* In the output from the previous command, we can see a revocation certificate being automatically generated and stored as `/home/foo/.gnupg/openpgp-revocs.d/8681B558A2B654933E69834CC8662846CCEA9E27.rev`
	* Although revoking a keypair is outside of the scope of this guide, instructions to do so can be found online[^6]

* The command to generate a revocation certificate can be done manually with the `gpg --gen-revoke` command
	* You may want to delete the automatically generated revocation certificate first before continuing with creating a manually generated one
	* Specify an output location with the `--output` flag; in this case, we are saving the output to a file with the *.asc* filetype extension
	* After generating the revocation certificate, you should use `chmod` to set the permissions of the file so only the owner can read and write to the file (`600`); this will mitigate the risk of an unauthorized party using the certificate if they gain access

> **Note**
> In this command, we used the email associated with our keypair's user ID, `foobar@baz.com`, to specify the key we want to act upon; in other commands, you can use multiple methods of specifying a user ID[^7]; there will be examples of each of these fields being used to identify a key throughout this guide

```console
foo@bar:~$ gpg --output revoke.asc --gen-revoke foobar@baz.com

sec  rsa4096/C8662846CCEA9E27 2023-01-01 Foo Bar (Good idea to add a comment here) <foobar@baz.com>

Create a revocation certificate for this key? (y/N) y
Please select the reason for the revocation:
  0 = No reason specified
  1 = Key has been compromised
  2 = Key is superseded
  3 = Key is no longer used
  Q = Cancel
(Probably you want to select 1 here)
Your decision? 0
Enter an optional description; end it with an empty line:
> Saving this here for a rainy day...
> 
Reason for revocation: No reason specified
Saving this here for a rainy day...
Is this okay? (y/N) y
ASCII armored output forced.
Revocation certificate created.

Please move it to a medium which you can hide away; if Mallory gets
access to this certificate he can use it to make your key unusable.
It is smart to print this certificate and store it away, just in case
your media become unreadable.  But have some caution:  The print system of
your machine might store the data and make it available to others!

foo@bar:~$ chmod 600 ~/revoke.asc

foo@bar:~$ ls -alh
...
-rw-------  1 foo foo  942 Jan 1 12:00 revoke.asc
...
```

> **Note**
> After generating your revocation certificate, store it in a secure location; as the output from the above command states, if someone gains access to this certificate they could publish it and invalidate your keypair
<br />

## Exporting Your Keys

* After generating your keypair, consider immediately backing it up by exporting your public and private to their own files with the `gpg --export` and `gpg --export-secret-keys` command, respectively
	* The `--armor` flag specifies that the key output format should be in human-readable ASCII format
	
> **Note**
> In this example, we redirected the standard output of the command to two .txt files; GPG can effectively accomplish the same result on its own by using the `--output` flag and specifying an output location; to become an effective user of Linux and/or Unix, it is highly suggested that you learn the fundamentals of pipes and redirection[^8]

```console
foo@bar:~$ gpg --armor --export-secret-keys foobar@baz.com > private.txt

foo@bar:~$ gpg --armor --export "Foo Bar" > public.txt
```
<br />

* Save both files in a secure private location, such as a USB key
	* It is best practice to use a USB key whose only contents are your two keys

> **Warning**
> If the private key is compromised at any point, someone with access to the key could impersonate the owner of the private key; this would be an appropriate circumstance to submit your revocation certificate to any key server you submitted it to
<br />

## Deleting Your Keys

* If at any point you realize you have made a fatal mistake and need to start over, run the following command to delete your private and public keypair
	* This command should be run **after** a revocation certificate for this key has been submitted to a key server
 	* Only servers known to have received public key need to receive the revocation certificate for said key
	* Once a key is deleted, it cannot be recovered without a backup

```console
foo@bar:~$ gpg --delete-secret-keys [uid]
```

[^1]:https://en.wikipedia.org/wiki/RSA_(cryptosystem)
[^2]:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-57pt1r5.pdf#%5B%7B%22num%22%3A193%2C%22gen%22%3A0%7D%2C%7B%22name%22%3A%22XYZ%22%7D%2C70%2C529%2C0%5D
[^3]:https://nvlpubs.nist.gov/nistpubs/specialpublications/nist.sp.800-57pt3r1.pdf#page=20&zoom=auto,-462,771
[^4]:https://www.keylength.com/en/4/
[^5]:https://csrc.nist.gov/glossary/term/Originator_usage_period
[^6]:https://blog.chapagain.com.np/gpg-revoking-your-public-key-and-notifiying-key-server/
[^7]:https://www.gnupg.org/documentation/manuals/gnupg-devel/Specify-a-User-ID.html
[^8]:https://www.baeldung.com/linux/pipes-redirection
