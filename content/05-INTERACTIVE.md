# Introducing the GPG Interactive Menu

* The GPG interactive menu is an interface connecting a user to each of the keys they have in their keyring (personal keypairs, public keys of other users, etc)

## Sections
- [Opening the GPG Interactive Menu](#opening-the-gpg-interactive-menu)
- [Situational Awareness](#situational-awareness)
- [Saving and Reverting Changes](saving-and-reverting-changes)

## Opening the GPG Interactive Menu

* When you have a keypair generated, you can try editing your key from the GPG interactive menu
	* To open this menu, run the `gpg --edit-key [uid]` command

```console
foo@bar:~$ gpg –-edit-key "Foo Bar"
gpg (GnuPG) 2.2.40; Copyright (C) 2022 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

sec  rsa4096/C8662846CCEA9E27
     created: 2023-01-01  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa4096/C8662846CCEA9E27
     created: 2023-01-01  expires: never       usage: E   
[ultimate] (1). Foo Bar (Good idea to add a comment here) <foobar@baz.com>

gpg> 
```
<br />

* Run the `help` command to see the full list of available commands
	* This menu has many interesting and useful commands to run on keys
	* While we do not go over the entire functionality of the GPG interactive menu in this guide, we will go over some of the most commonly used features

> **Note**
> Many commands from the GPG Interactive Menu have a CLI equivalent that can be run outside the menu; at various points in this guide I will reference these command equivalents, but you should always remember to use the appropriate `save` or `quit` command when exiting the menu to try out these commands yourself (better yet, open another terminal to run them in parallel)

```console
gpg> help
quit        quit this menu
save        save and quit
help        show this help
fpr         show key fingerprint
grip        show the keygrip
list        list key and user IDs
uid         select user ID N
key         select subkey N
check       check signatures
sign        sign selected user IDs [* see below for related commands]
lsign       sign selected user IDs locally
tsign       sign selected user IDs with a trust signature
nrsign      sign selected user IDs with a non-revocable signature
adduid      add a user ID
addphoto    add a photo ID
deluid      delete selected user IDs
addkey      add a subkey
addcardkey  add a key to a smartcard
keytocard   move a key to a smartcard
bkuptocard  move a backup key to a smartcard
delkey      delete selected subkeys
addrevoker  add a revocation key
delsig      delete signatures from the selected user IDs
expire      change the expiration date for the key or selected subkeys
primary     flag the selected user ID as primary
pref        list preferences (expert)
showpref    list preferences (verbose)
setpref     set preference list for the selected user IDs
keyserver   set the preferred keyserver URL for the selected user IDs
notation    set a notation for the selected user IDs
passwd      change the passphrase
trust       change the ownertrust
revsig      revoke signatures on the selected user IDs
revuid      revoke selected user IDs
revkey      revoke key or selected subkeys
enable      enable key
disable     disable key
showphoto   show selected photo IDs
clean       compact unusable user IDs and remove unusable signatures from key
minimize    compact unusable user IDs and remove all signatures from key

* The 'sign' command may be prefixed with an 'l' for local signatures (lsign),
  a 't' for trust signatures (tsign), an 'nr' for non-revocable signatures
  (nrsign), or any combination thereof (ltsign, tnrsign, etc.).
```

* This may look intimidating, but as stated before, mastery of all these commands is not required to become a proficient user of GPG
	* We are introducing the `help`, `save`, `list`, `pref`, `showpref`, and `quit` commands in this chapter
	* We will focus on the `addphoto`, `showphoto`, `adduid`, `uid`, and `deluid` commands in [Chapter 6](/content/06-IMAGE.md#personalizing-your-key)
 	* We will focus on the `fpr` and `sign`, commands in [Chapter 8](/content/08-SIGNKEYS.md#signing-keys)
<br />

## Situational Awareness

* Before proceeding any further, let's gather some additional information about our key
	* Let's start by issuing the `list` command, which displays the user and key ID for our selected key
 	* Each key has a single 32 bit key ID assigned to it; these are not to be confused with *fingerprints*[^1], and since the former is vulnerable to collision[^2], they are not to be used as the primary method of identifying keys
    * Each key can 

```console
gpg> list

sec  rsa4096/C8662846CCEA9E27
     created: 2023-01-01  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa4096/0B19687D57050956
     created: 2023-01-01  expires: never       usage: E   
[ultimate] (1). Foo Bar (Good idea to add a comment here) <foobar@baz.com>
```
<br />

* Next, let's issue the `pref` and `showpref` commands
    * These commands show what algorithms our public key should inform other OpenPGP-adhering software
    * The `pref` command shows the two-character ID for supported algorithms[^3] and shortened names for additional features
    * The `showpref` command shows the more verbose output of the previous command

```console
gpg> pref
[ultimate] (1). Foo Bar (Good idea to add a comment here) <foobar@baz.com>
     S9 S8 S7 S2 H10 H9 H8 H11 H2 Z2 Z3 Z1 [mdc] [no-ks-modify]

gpg> showpref
[ultimate] (1). Foo Bar (Good idea to add a comment here) <foobar@baz.com>
     Cipher: AES256, AES192, AES, 3DES
     AEAD:
     Digest: SHA512, SHA384, SHA256, SHA224, SHA1
     Compression: ZLIB, BZIP2, ZIP, Uncompressed
     Features: MDC, AEAD, Keyserver no-modify
```
<br />

## Saving and Reverting Changes

* After we make edits to a key in the interactive menu, we can either choose to save the modifications or revert changes
	* Run the `save` command to save any wanted changes
	* Run the `quit` command to exit the interactive menu without saving any changes

```console
gpg> save
```

```console
gpg> quit
```

[^1]:https://en.wikipedia.org/wiki/Public_key_fingerprint
[^2]:https://evil32.com/
[^3]:https://datatracker.ietf.org/doc/html/rfc4880#section-9
