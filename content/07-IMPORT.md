# Importing Public Keys

* This short chapter reviews how to import keys to a GPG keyring
	* As stated previously, software adhering to the OpenPGP standard requires users to import the public keys of their intended recipients to their keyring to run commands like encryption and verifying signatures	

## Importing Keys

* When another user shares their public key with you, first import it by running the `gpg --import` command, passing the absolute/relative path of the key you mean to import as a parameter
	* In this example, we use PGP sample keys provided by IETF[^1]

```console
foo@bar:~$ gpg –-import alice.gpg
gpg: key F231550C4F47E38E: public key "Alice Lovelace <alice@openpgp.example>" imported
gpg: Total number processed: 1
gpg:               imported: 1
```
<br />

* List out the public keys in your keyring to verify that the key was successfully imported

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
uid           [ultimate] [jpeg image of size 5217]
sub   rsa4096 2023-01-01 [E]

pub   ed25519 2019-01-22 [SC]
      EB85BB5FA33A75E15E944E63F231550C4F47E38E
uid           [ unknown] Alice Lovelace <alice@openpgp.example>
sub   cv25519 2019-01-22 [E]
```

* The public key of Alice was imported!

[^1]:https://www.ietf.org/archive/id/draft-bre-openpgp-samples-01.html
