# Personalizing Your Key

* Cybersecurity experts with asymmetric keypairs will often personalize their public key in a way that makes the owner of said key easily identifiable when exchanging keys
	* A simple and popular way to do this is to attach a digital image of yourself to your public key
    * You can also add multiple user IDs to your key for easy identification

## Sections
* [The GPG `addphoto` command](#the-gpg-addphoto-command)
* [Adding User IDs](#adding-user-ids)

## The GPG `addphoto` Command

* From the interactive menu, run the `addphoto` command
	* According to GPG source code[^1], "[keeping] the image close to 240x288 is a good size to use."

> **Note**
> If your selected image is a *.png* file, you can convert it to a *.jpg* file by installing *ImageMagick*[^2], a CLI tool that converts image files; issue the `mogrify -format jpg [image_path]` command to convert the file to a JPG format

```console
gpg> addphoto [image_path]
```
<br />

* After specifying the image file path, a new window should open with your selected image on display
	* Exit the window to complete the process
    * You can run the `showphoto` command to verify the correct image was added again
    * Do not forget to `save` your progress

```console
gpg> addphoto photo.jpeg
Is this photo correct (y/N/q)? y

sec  rsa4096/C8662846CCEA9E27
     created: 2023-01-01  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa4096/C8662846CCEA9E27
     created: 2023-01-01  expires: never       usage: E   
[ultimate] (1). Foo Bar (Good idea to add a comment here) <foobar@baz.com>
[ unknown] (2)  [jpeg image of size 5217]

gpg> save
```
<br />

## Adding User IDs

* It's not uncommon for people to maintain multiple online "identities" in their everyday life
    * e.g you may have a school email that you use for study-related accounts only, a Google email account for Google services, a personal email account you use only for communication with friends and family, etc
    * With all these identities in play, it may be useful to consolidate all/most of them within a single GPG key you use for encrypted communications
* Issue the `adduid` command to begin the process of creating a new user ID to be associated with a given keypair


```console
gpg> adduid
Real name: Foo Bar 2
Email address: foobar2@baz.edu
Comment: Goofy comment goes here
You selected this USER-ID:
    "Foo Bar 2 (Goofy comment goes here) <foobar2@baz.edu>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O

sec  rsa4096/C8662846CCEA9E27
     created: 2023-01-01  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa4096/0B19687D57050956
     created: 2023-01-01  expires: never       usage: E   
[ultimate] (1)  Foo Bar (Good idea to add a comment here) <foobar@baz.com>
[ unknown] (2). Foo Bar 2 (Goofy comment goes here) <foobar2@baz.edu>
```
<br />

* If we want to remove a user ID, we first have to select it with the `uid` command
    * Specify the user ID to act on by its index, which according to the output from the previous command, is `2`

```console
gpg> uid 2

sec  rsa4096/C8662846CCEA9E27
     created: 2023-06-16  expires: never       usage: SC
     trust: ultimate      validity: ultimate
ssb  rsa4096/0B19687D57050956
     created: 2023-06-16  expires: never       usage: E
[ultimate] (1)  Foo Bar (Good idea to add a comment here) <foobar@baz.com>
[ unknown] (2)* Foo Bar 2 (Goof comment goes here) <foobar2@baz.edu>
```
<br />

* With the key selected (given by the `*` character), we can run the `deluid` command

```console
gpg> deluid
Really remove this user ID? (y/N) y

sec  rsa4096/C8662846CCEA9E27
     created: 2023-01-01  expires: never       usage: SC  
     trust: ultimate      validity: ultimate
ssb  rsa4096/0B19687D57050956
     created: 2023-01-01  expires: never       usage: E   
[ultimate] (1)  Foo Bar (Good idea to add a comment here) <foobar@baz.com>
```

[^1]:https://github.com/gpg/gnupg/blob/e16fc3e19c3fbef1e22099b40a92dafd832ff05c/po/tr.po#L5131
[^2]:https://imagemagick.org/
