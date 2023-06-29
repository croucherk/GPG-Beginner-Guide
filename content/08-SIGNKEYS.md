# Signing Keys

* Key signing is an integral part of OpenPGP software like GPG; signing a key is a representation of trust: 
	* The user (Bob) trusts that the owner of this key (Alice) is who they say they are, and they are willing to add their private key signature to this key for others to read
	* If another user trusts Bob enough, they may see Bob's signature on Alice's key and choose to transitively trust Alice as well
* This chapter reviews how to use fingerprints to validate keys and how to sign keys

## Sections
- [Validating a Public Key with its Fingerprint](#validating-a-public-key-with-its-fingerprint)
- [Signing a Key](#signing-a-key)

## Validating a Public Key with its Fingerprint

* Before signing **any** key, you should first verify that you have the correct key
	* Each OpenPGP-compliant key has an associated fingerprint assigned to it
	* You can use this fingerprint to verify its authenticity with its owner; this can be done in-person, over the phone, via SMS/email, or any other appropriate means of communication
* Enter the GPG Interactive Menu of Alice's key, then run the `fpr` command

> **Note**
> The same command can be run directly from the CLI by running the `gpg --fingerprint [key_id]` command

```console
foo@bar:~$ gpg --edit-key alice@openpgp.example
gpg (GnuPG) 2.2.40; Copyright (C) 2022 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.


pub  ed25519/F231550C4F47E38E
     created: 2019-01-22  expires: never       usage: SC  
     trust: unknown       validity: unknown
sub  cv25519/4766F6B9D5F21EB6
     created: 2019-01-22  expires: never       usage: E   
[ unknown] (1). Alice Lovelace <alice@openpgp.example>

gpg> fpr
pub   ed25519/F231550C4F47E38E 2019-01-22 Alice Lovelace <alice@openpgp.example>
 Primary key fingerprint: EB85 BB5F A33A 75E1 5E94  4E63 F231 550C 4F47 E38E
```

* Now that we have validated this key, we can continue with signing it
<br />

## Signing a Key

* From the GPG Interactive Menu of the key you mean to sign, run the `sign` command

> **Note**
> The same command can be run directly from the CLI by running the `gpg --sign-key [key_id]` command

```
gpg> sign

pub  ed25519/F231550C4F47E38E
     created: 2019-01-22  expires: never       usage: SC  
     trust: unknown      validity: unknown
 Primary key fingerprint: EB85 BB5F A33A 75E1 5E94  4E63 F231 550C 4F47 E38E

     Alice Lovelace <alice@openpgp.example>

Are you sure that you want to sign this key with your
key "Foo Bar (Good idea to add a comment here) <foobar@baz.com>" (C8662846CCEA9E27)

Really sign? (y/N) y
```

* GPG will automatically sign the key you are editing with a designated signing key
	* Because we only have one complete keypair in our keyring now, it will default to that key
	* If you have multiple keypairs, you can designate the one to use for signing with `gpg --default-key`
	* Let's skip this for now, since we only have one keypair

<br />

* GPG has added Alice's key to its valid keys keyring, validate this by running the `gpg --list-keys` command

```console
foo@bar:~$ gpg --list-keys
...
pub   ed25519 2019-01-22 [SC]
      EB85BB5FA33A75E15E944E63F231550C4F47E38E
uid           [  full  ] Alice Lovelace <alice@openpgp.example>
sub   cv25519 2019-01-22 [E]
```
<br />

* We can also list the signatures on a key by running the `gpg --list-sigs [key_id]` command

```console
foo@bar:~$ gpg --list-sigs Alice
pub   ed25519 2019-01-22 [SC]
      EB85BB5FA33A75E15E944E63F231550C4F47E38E
uid           [  full  ] Alice Lovelace <alice@openpgp.example>
sig 3        F231550C4F47E38E 2019-10-15  Alice Lovelace <alice@openpgp.example>
sig          C8662846CCEA9E27 2023-01-01  Foo Bar (Good idea to add a comment here) <foobar@baz.com>
sub   cv25519 2019-01-22 [E]
sig          F231550C4F47E38E 2019-01-22  Alice Lovelace <alice@openpgp.example>
```

* We can see from the output above that our key's signature has been added to Alice's public key
