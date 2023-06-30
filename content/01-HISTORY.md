# A Brief History Lesson<a name="history"></a>

* Before we dive into the technical details, let us briefly explore the connected stories behind PGP, OpenPGP, and GPG

## Sections
- [The Origins of Pretty Good Privacy](#the-origins-of-pretty-good-privacy)
- [The OpenPGP Email Encryption Standard](#the-openpgp-email-encryption-standard)
- [The Successor to PGP: GNU Privacy Guard](#the-successor-to-pgp-gnu-privacy-guard)
- [Bringing it All Together](#bringing-it-all-together)

## The Origins of Pretty Good Privacy

* *Pretty Good Privacy* (PGP)[^1] is an encryption program that provides end-to-end encryption for the encryption, decryption, and digital signing of emails, files, and more

<img src="/content/img/phil-zimmerman.jpg" alt="drawing" width="400"/>

* Phil Zimmerman[^2], the creator of PGP, released the software in June 1991
    * Zimmerman was a politically active individual, and developed PGP with the goal of it being used as a human rights tool in USA[^3][^4]
    * Political activists on online forums could use PGP to securely exchange messages on *bulletin board systems* (BBS)[^5]
    * The tool eventually became used outside of USA

* For his work in developing and distributing PGP, Zimmerman was subject to a criminal investigation for "munitions export without a license"
    * At the time[^6], the US Government defined any cryptosystem using keys larger than 40 bits as "munitions"
    * Because PGP keys were at least 128 bits, it therefore qualified as a munition
    * Having proliferated outside USA borders, the USA formally opened an investigation against Zimmerman in February 1993

* In response to the investigation, Zimmerman wrote and published *The Official PGP User's Guide by Phil Zimmerman*[^7]
	* Published via *MIT Press*[^8], the book contained the entire source code of PGP, making the code accessible to anyone
	* Because the export of books are protected by the *First Amendment to the United States Constitution*[^9], the charges of exporting without a license became moot, and the investigation was closed in January 1996[^10] without filing criminal charges

* Zimmerman and his team merged with the company Viacrypt in 1996, and changed the name to PGP Inc., where they continued development of PGP
	* After many acquisitions, the source code of PGP eventually became owned by Broadcom Inc.[^11] in 2019, where it is being distributed as a paid closed-source product[^12]
<br />

## The OpenPGP Email Encryption Standard

* *OpenPGP*[^13] is an open-source[^14] protocol derived from the original PGP software

* While working at PGP Inc., Zimmerman and his team had to navigate restrictive licensing and patent concerns
    * These constraints would often pigeon-hole them into using specific encryption algorithms, or have them live under the threat of the license to a working algorithm expiring
    * Due to PGP's importance to the cryptographic community, Zimmerman and co. submmitted a *Request for Comments*[^15] to the *Internet Engineering Task Force* (IETF)[^16] in July 1997 detailing the PGP encryption protocol, essentially making PGP's logic "free to use"
	* The acceptance of the proposal by IETF led to the formation of the *OpenPGP Working Group*

* OpenPGP is actively being developed, with the ultimate goal of qualifying as an *Internet Standard*[^17]
	* The most current OpenPGP Proposed Standard is defined in *RFC 4880*[^18], and is the message format implemented by all OpenPGP-supporting software, including GPG
<br />

## The Successor to PGP: GNU Privacy Guard

* GPG[^19] is a free open-source software for encrypting and signing data
	* GPG makes use of a key management system, a command-line interface, *public-key cryptography*[^20], full support for dozens of encryption algorithms, and many more useful features
 	* GPG is compatible with multiple operating systems[^21]; the scope of this guide is Debian-based Linux operating systems
  	* According to RFC 4880, the RFC which defines OpenPGP, "GnuPG is an OpenPGP implementation that avoids all encumbered algorithms"; what this means is that at the time of writing RFC 4880, GPG did not support algorithms with patent/licensing concerns such as RSA, though modern versions of GPG do in fact support many of these algorithms

* GPG is part of the *GNU Project*[^22], a project launched by Richard Stallman[^23] in 1983
    * The GNU Project's mission is to create a "Unix-compatible system that would be 100% free software"
    * This same project is responsible for the creation of the *Linux kernel*[^24]
	* As it is part of the GNU Project, GPG is a *free software*[^25], and under the *GNU General Public License*[^26], it can be freely used, modified, and distributed
<br />

## Bringing it All Together

* To illustrate the differences between PGP, OpenPGP, and GPG, refer to the table below <br /><br />

| - | PGP | OpenPGP | GPG |
| - | - | - | - |
| What is it? | Encryption software | A protocol for encrypting data | Encryption software |
| When was it released/published? | 1991 | 1997 | 1999 |
| Is it open source? | No (previously, yes) | Yes | Yes |
| Who currently manages it? | Broadcom Inc. | The OpenPGP Working Group | The GnuPG Project |

[^1]:https://en.wikipedia.org/wiki/Pretty_Good_Privacy
[^2]:https://en.wikipedia.org/wiki/Phil_Zimmermann
[^3]:https://philzimmermann.com/EN/background/peace.html
[^4]:https://philzimmermann.com/EN/essays/WhyIWrotePGP.html
[^5]:https://en.wikipedia.org/wiki/Bulletin_board_system
[^6]:https://en.wikipedia.org/wiki/Export_of_cryptography_in_the_United_States#PC_era
[^7]:https://archive.org/details/officialpgpusers0000zimm
[^8]:https://en.wikipedia.org/wiki/MIT_Press
[^9]:https://en.wikipedia.org/wiki/First_Amendment_to_the_United_States_Constitution
[^10]:https://philzimmermann.com/EN/news/PRZ_case_dropped.html
[^11]:https://en.wikipedia.org/wiki/Broadcom_Inc.
[^12]:https://en.wikipedia.org/wiki/Comparison_of_open-source_and_closed-source_software
[^13]:https://www.openpgp.org/
[^14]:https://opensource.com/resources/what-open-source
[^15]:https://www.ietf.org/standards/rfcs/
[^16]:https://en.wikipedia.org/wiki/IETF
[^17]:https://en.wikipedia.org/wiki/Internet_Standard
[^18]:https://datatracker.ietf.org/doc/html/rfc4880
[^19]:https://gnupg.org/index.html
[^20]:https://en.wikipedia.org/wiki/Public-key_cryptography
[^21]:https://gnupg.org/download/index.html
[^22]:https://www.gnu.org/
[^23]:https://en.wikipedia.org/wiki/Richard_Stallman
[^24]:https://en.wikipedia.org/wiki/Linux_kernel
[^25]:https://www.gnu.org/philosophy/free-sw.html
[^26]:https://www.gnu.org/licenses/gpl-3.0.html
