This reference guide is to help new users of the [GNU Privacy Guard (GPG)](https://gnupg.org/) command line tool. The current version of this tool at the time of the release of this guide is GPG 2.3. This is not a cumulative review of all GPG commands, but a selective and targeted approach to understanding the fundamentals of GPG and its appropriate usages. When I first wrote this guide, I had just completed my first semester as an undergraduate teaching assistant for Foundations of Cybersecurity at Northeastern University's Khoury College of Computer and Information Sciences, and observed consistent occurrences of the same questions from students and new TA's alike. To address this deficiency, I wrote a simple GPG guide that proved successful the next semester I assisted, and am now publishing it to GitHub for the public to use.

# Introduction
GPG is a free open-source implementation of the OpenPGP standard commonly known as PGP, the de facto standard in communication security. GPG is used by members of the cybersecurity industry to generate asymmetric keypairs―a shared public key and a secret private key―which are used to send strongly encrypted messages. For example, if Alice wants to send a secure message to Bob, Alice will encrypt her message with Bob’s public key, send the message, and Bob will decrypt the message with his private key. Communications picked up by eavesdroppers in transit will be unreadable since Bob keeps his private key secret **under all circumstances**, and his private key is computationally exhaustive to guess.
Using this guide, newcomers will learn the fundamental commands of GPG and learn what common mistakes to avoid, ultimately gaining the minimum required literacy to use the program with ease.
# Installing GPG
GPG comes preinstalled on most recent and popular GNU/Linux distributions (Debian, Fedora, RedHat, Ubuntu, etc). Older GNU/Linux distributions will have to manually install the program. To install GPG 2.2 on Ubuntu 20.04 systems (the OS from Project 1: Linux Basics, and the OS of choice for this course) run the following command in a command line interface (CLI):
```console
$ sudo apt install gnupg xloadimage
```
The xloadimage application is a graphics file viewer dependency for GPG that does not come preinstalled on most modern Linux distributions and is therefore suggested to install alongside GPG. GPG can be installed on additional OS’s such as Windows, macOS, Android, and others by visiting the [Download](ttps://gnupg.org/download/index.html) page of the GPG website.
After installing GPG, verify that it is ready to execute via the CLI by running the following command:
```console
$ gpg --help
```
This command should print a menu to the CLI providing relevant information, such as its version, license, supported algorithms, proper usage syntax, and a comprehensive list of all available commands for GPG. As stated previously, you do not need to memorize every aspect of this menu for the purpose of this course or if you are following along with this guide, but you are encouraged to improve your own GPG literacy at your leisure.

# Generating a Keypair
The first step in using GPG is almost always to generate an asymmetric keypair. Run the following command:
```console
$ gpg –-full-generate-key
```
You will be asked to specify your encryption algorithm, key length, a passphrase, and optionally an expiration date for your keys. Choose the RSA/RSA option for your public/private keys, and a key length of 4096 bits (Since 2015, NIST’s minimum key length recommendation for RSA algorithms is 2048 bits). You may choose to use an expiration date of your keys (Since 2020, NIST originator usage period recommendation for private signature keys has been 1-3 years) so long as the expiration period is explicitly after the end of the CY2550 course you are currently taking. GPG will ask you for your real name and email address to craft a user ID (UID) to uniquely
identify your key. Your selected passphrase is optional, but encouraged since it will increase the security of your keypair and increase the complexity of the encryption algorithm used to generate your keypair. To verify the generation process completed successfully, run the following command to list public and private keys, respectively:
```console
$ gpg –-list-keys gpg –-list-secret-keys
```
Your keys are now a part of your virtual GPG keyring. Your keys are uniquely identifiable by your name or email address, also known as your UID. When using commands that require a UID input, try entering the name or email address of the key you are operating on.
After generating your keypair, immediately back it up by exporting your keys to their own files and saving in a secure private location, such as a USB key (it is best practice to use a USB key whose only contents are your two keys). Use the “-armor” flag to specify that the key output format should be in human-readable ASCII format. The commands to export public and private keys are given, respectively.
```console
$ gpg –-armor –-export-secret-keys [uid] > [output filename]
$ gpg --armor --export [uid] > [output filename]
```
Be careful when exporting your private key! If it is compromised at any point during transit, the security of you and everyone you have exchanged your public key with has likely been compromised as well. 
If at any point you realize you have made a fatal mistake and need to start over, run the following command to delete your private and public keypair:
```console
$ gpg --delete-secret-keys [uid]
```
Do not use this command thoughtlessly! Once a key is deleted, it cannot be recovered without a backup. If you have already exchanged your public key at the time of deletion, you should notify those members through an appropriately secure channel.

# GPG Interactive Menu
When you have a keypair generated, you can try editing your key--or others you have imported to your keyring--in the GPG interactive menu. To open this menu, run the following command:
```console
$ gpg –-edit-key [uid]
```
This menu has an assortment of interesting and useful commands to run on individual public keys. If at any point in the interactive menu you need to see a list of available commands, just run the following command:
```console
$ help
```
Cybersecurity experts with asymmetric keypairs will often personalize their public key in a way that makes the owner of said key easily identifiable when exchanging keys. A simple way to do this is to attach a digital image to your public key. To do this, first find a JPG/PNG image file with a clear visual of your face (to authenticate your identity) and save it onto your Linux machine. Then, from the interactive menu, run the following command:
```console
$ addphoto [filename path]
```
Your photo size should strike a balance between being large enough to read and small enough not to throw an error. GPG will display a warning if the photo you attempt to add is larger than 6144 bytes. The source code of GPG actually suggests keeping the image close to 240x288, and many other PGP programs suggest 120x144 as an ideal image size.
Another important command to consider for the GPG interactive menu is the signing command. You will hopefully remember from lectures that unlike the World Wide Web, which uses certificate authorities to sign certificates to credible HTTPS servers/websites, the PGP community relies on a web of trust protocol to validate its members. If for example, Alice wants to know if she can trust Charlie, she may look at his public key and see if her friend Bob has signed Charlie’s key. Seeing Bob’s signature on Charlie’s key, Alice knows that Bob trusts Charlie, and since Alice trusts Bob, she now trusts Charlie via the transitive property. In the real world, to sign a public key one must import it to their key ring, open the interactive menu for that specific key, and run the following command:
```console
$ sign
```
Key signing can also be done quickly from the CLI with the following command:
```console
$ gpg --sign-key [uid]
```
Unlike encryption, where a public key creates a ciphertext that can only be decrypted to plaintext by a private key, PGP signing works in reverse: a user signs a key/file with their private key, sends that key/file to a recipient, and the recipient reads the digital signature with the public key of the sender, which they should already have in their keyring. That’s why when another member of the PGP community signs your public key and returns it to you before you import their public key to your keyring, the signature will appear as unknown, since their public key is required to make the signature human readable. When signing other students’ keys, be sure to import their keys to your keyring and expect them to do the same with your key, otherwise the signatures on your/their keys will be unverifiable. You can check what signatures are currently on your key by running the following command:
```console
$ gpg --list-sigs
```
This command will list all keys currently on your keyring, whether they are verified or not. To only list verified keys, run the following command:
```console
$ gpg --check-sigs
```
When you are satisfied with the work you have done in the GPG interactive menu, you may either save or discard your progress and then leave. To save your changes to a public key, run the following command:
```console
$ save
```
If you are unsatisfied with your changes, run the following command instead:
```console
$ quit
```

# Exchanging Keys and Communicating Securely
To securely communicate with another party, you need a copy of their public key. You will remember from the Alice/Bob metaphor that the public key is used to encrypt a message while its corresponding private key is used to decrypt the message. You can export your own public key for sharing by using the “--export” option. When another user shares their public key with you, first import it by running the following command:
```console
$ gpg –-import [file path]
```
You should now have that user’s key in your keyring. List out the public keys in your keyring to verify this. Whenever you want to exchange securely encrypted files or messages with this user, simply use their public key to encrypt the file by using the following command:
```console
$ gpg --armor --recipient [uid] --encrypt [file]
```
If you want your recipient to know that it was you who sent them a file, you should digitally sign the file with your private key. The recipient will compare the hash generated by the private key to the hash found on your public key, which they should have in their keyring. If the hashes are the same, then they have verified that you were in fact the one who sent the file. A file can be digitally signed by running the following command:
```console
$ gpg --armor --sign [file]
```

# FAQ
This section is dedicated to addressing common issues related to GPG encountered by new users. If you feel that your question deserves to be included in the FAQ of this document, you are invited to email the author of this document.
* How do I run multiple cryptographic operations on a single file?

Multiple GPG commands can be condensed into one line by appending multiple options after the call to GPG. This has been done before in examples that include the options “armor” and “sign” but can be expanded to include options such as “encrypt”.
* I am trying to import a private key but am receiving the error message, “no space left on device”. What does this mean?

When you created your virtual machine, you allotted a specific amount of disk space for that machine to occupy on your host machine. When that space is consumed, your virtual machine will prevent any additional files being imported onto the disk. Run the command “df -h/” to reveal how much disk space is being used. If it is at 100% use, consider either expanding your disk space from the host machine’s settings menu for that virtual machine or creating a new virtual machine with more disk space for future use.
* Why won’t GPG accept my image file in the interactive menu?

This could be one of two issues. Firstly, experienced users of GPG recommend adding photos no larger than 400 x 400 pixels, otherwise you may cause GPG to crash. Additionally, GPG uses an internal tool called “xloadimage” to import photos, but the program assumes that most OS’s have this tool preinstalled, so it is not included on its dependency list during installation. Your virtual machine might not have this tool preinstalled, so try manually installing it by running the command `sudo apt install xloadimage` and then try to add the image once more.
* When I run commands with the `--armor` option, I receive the error message `usage: gpg [options] --encrypt [filename]`. What does this mean?

When using the `--armor` option, be sure to append that specific option immediately after the call to GPG, then include your additional options like `-sign`, `--encrypt`, and `--recipient`. Flags like `--armor` are syntactically expected before options in GPG.
* I have imported the public key of a partner who has signed my own key, but after I imported that key, I received the error `# signatures not checked due to missing keys`. What does this mean?

Your partner likely had their public key signed by some number of other users who have not signed your key/whose public keys are not in your keyring. You do not need to immediately remediate this error since it will not prevent you from importing the first public key, but you are invited to reach out to those users and expand your web of trust by signing and importing their key.

# Conclusion
I hope that you find this reference document useful. You should now possess the base knowledge of what you need to successfully implement strong asymmetric encryption and to avoid common mistakes. Good luck, and happy hacking!
