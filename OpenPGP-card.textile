h1. OpenPGP Card

The OpenPGP Card is an ISO/IEC 7816-4/-8 compatible smart card implementation that is integrated with many GnuPG functions.
Using this smart card, various cryptographic tasks (encryption, decryption, digital signing/verification, authentication etc.) can be performed.

The cards come in various form factors ranging from the standard size ID-1, over ID-1 with cut-outs for ID-000 (i.e. SIM card size), which together with an USB card reader allows to build a do-it-yourself crypto stick, to the Nitrokey USB security key.

They implement the OpenPGP Card specification which evolved compatibly from "v1.0":http://g10code.com/docs/openpgp-card-1.0.pdf in 2003, via "v1.1":http://g10code.com/docs/openpgp-card-1.1.pdf in 2004, "v2.0":http://g10code.com/docs/openpgp-card-2.0.pdf which was released in 2009, to "v3.4.1":https://www.gnupg.org/ftp/specs/OpenPGP-smart-card-application-3.4.1.pdf in 2020.

Version 1.0 of the specification is mostly of theoretical interest, as most - if not all - cards adhere to version 3.

All versions allow storing card holder details as well as generating and storing up to three RSA key pairs on the card.

While the 1.x version only supported 1024-bit RSA keys and no certificates, version 2.0 allows for RSA keys up to 4096 bits (requires GnuPG 2.0.18+) and optionally an X.509 card holder certificate for the AUT key on the card. Version 3.0 introduced support for ECC.

Other changes were:
* V1.1 brought 4 optional DOs for private use with different access conditions
* V2.0 brought optional support for
  * card reset functionality (life cycle management)
  * SecureMessaging
  * changeable algorithm attributes
  * other algorithms than RSA (not specified)
  * Removal of PW2 ("Encryption PIN") present in v1.1. In v2.0 only "Admin PIN" and "User PIN" are specified. Furthermore, v2.0 spec defines "user consent" capabilities for signature key.


h2. Where/How to get one?

OpenPGP Cards / Nitrokeys can be obtained from vendors like e.g.
* "Nitrokey":https://shop.nitrokey.com/
* "Kernel concepts":http://shop.kernelconcepts.de
* or by becoming a fellow in the "Free Software Foundation Europe":http://fsfe.org which uses the OpenPGP Card specification on the "Fellowship Smart Cards":http://wiki.fsfe.org/FellowshipSmartCard it hands out to its fellows.


h2. State of OpenSC support

OpenPGP Card "v1.0":http://www.g10code.de/docs/openpgp-card-1.0.pdf /"1.1":http://www.g10code.de/docs/openpgp-card-1.1.pdf is deprecated but should work since OpenSC 0.9.1. Starting with OpenSC 0.12.2, OpenSC supports reading the OpenPGP Card "v2.0":http://www.g10code.de/docs/openpgp-card-2.0.pdf too. Since OpenSC 0.13 full write support for OpenPGP Card 2 is supported. Support for OpenPGP Card 3 was introduced later.

h2. Usage

To apply this usage to Gnuk, the patch above is needed. Basically, usage between general OpenPGP Card (for example Nitrokey) and Gnuk are the same, except some differences will be noted below.

h3. 1. Display user info

Use @openpgpg-tool@

h3. 2. Read and write data object (DO)

Use @opensc-explorer@.
For example, you want to change card holder name:

1. Run @opensc-explorer@

2. Verify Admin PIN by this command
<pre><code>verify CHV3 3132333435363738</code></pre>

in which @CHV3@ means Admin PIN is to be verified (User PIN will be CHV1 or CHV2) and @3132333435363738@ is ASCII-decoded hex string of PIN code "12345678".

3. Put data to 005B DO, the DO containing card holder name:
<pre><code>do_put 005B "Quan"</code></pre>

4. Remove it afterwards:
<pre><code>do_put 005B ""</code></pre>

5. Or change user PIN to "654321":
<pre><code>change CHV1 31:32:33:34:35:36 "654321"</code></pre>
Where @31:32:33:34:35:36@ is hex string of ASCII-decoded old PIN “123456”.

Reading DO content is not as straightforward as writing, because the DOs are nested in each other. For example, to read 005B DO, you have to go through 0065 DO:
<pre><code>OpenSC [3F00]> cd 0065
OpenSC [3F00/0065]> cat 005B
00000000: 51 75 61 6E Quan
</code></pre>

**Note**: We cannot delete DO content with <code>delete/rm</code> command. Technical reason: The OpenSC framework doesn't pass the full path of file to OpenPGP driver, so the driver cannot identify the DO to be deleted.

h3. 3. Generating keys

h5. Key generation via @openpgp-tool@:

<pre><code>openpgp-tool --verify CHV3 --pin 12345678 --gen-key 3
openpgp-tool --verify CHV3 --pin 12345678 --gen-key 1 --key-len 1024
</code></pre>

In which, @--genkey 3@ means that we're generating key with ID=3. The three keys in the have these IDs: Singing key: 1, Decryption key: 2, Authentication: 3.

@--key-length 1024@ means that the key is 1024-bit. We can specify bit length: 1024, 2048, 3072, 4096.
If this option is absent, default key length 2048-bit is used.
+Gnuk:+ Gnuk only supports 2048-bit key, so don’t specify @--key-length@ option, you also have to delete old key before generating or import new one.
<pre><code>openpgp-tool --verify CHV3 --pin 12345678 --del-key 3</code></pre>

h5. Key generation via @pkcs15-init@:

<pre><code>pkcs15-init --delete-objects privkey,pubkey --id 3 --generate-key rsa/2048 --auth-id 3 --verify
</code></pre>

There is limitation: @pkcs15-init@ requires new key length to be the same as existing key. To generate key with different key length, @openpgp-tool@ is recommended.

@pkcs15-init@ also requires to explicitly remove existing key/object. That's why we have @--delete-objects privkey,pubkey --id 3@ in the command (though it has no effect to Nitrokey, which does not support deleting key, but support overwriting key).

h3. 4. Delete key (Gnuk)

Deleting key is supported by Gnuk only. Nitrokey does not.
Example to delete 3rd (authentication) key:

bc. openpgp-tool --verify CHV3 --pin 12345678 --del-key 3

or

bc. pkcs15-init --delete-objects privkey,pubkey --id 3

If you want to delete key from Nitrokey, the only option is to erase card (all things will be deleted).

h3. 5. Erase card (Nitrokey)

Erasing card is supported by Nitrokey (or general OpenPGP Card v2+) only. Gnuk does not support.

bc. openpgp-tool --erase

or

bc. pkcs15-init --erase-card

h3. 6. Import key resp. certificate

h5. Only certificate

bc. pkcs15-init --store-certificate mycert.pem --id 3

In which the PEM file is extracted from p12 using OpenSSL (key is stripped out):

bc. openssl pkcs12 -in myprivate.p12 -nokeys -out mycert.pem

Note that the OpenPGP Card v2 contains only 1 certificate, so the ID to store is always 3.

h5. Only key:

bc. pkcs15-init --delete-objects privkey,pubkey --id 3 --store-private-key mykey.pem --auth-id 3 --verify-pin --id 3

In which the PEM file is extracted from a p12 file using OpenSSL
(certificate is stripped out):

bc. openssl pkcs12 -in myprivate.p12 -nocerts -out mykey.pem

h5. Pairs of key & certificate from P12 file:

bc. pkcs15-init --delete-objects privkey,pubkey --id 3 --store-private-key myprivate.p12 --format pkcs12 --auth-id 3 --verify-pin

*Notes:*
 * In current version, @pkcs15-init@ tool is pretty silent, so you may not recognize if the operation is successful or not. You should run @pkcs15-init@ in debug mode (set environment variable @OPENSC_DEBUG=3@).
 * In p12 file, the @pkcs15-init@ detect X.509 certificates in hierarchy, in which only the first found certificate need to be imported. But @pkcs15-init@ then try to do with all, so the later imports will fail. You can see some error messages due to this failure, but it is OK because the main certificate has been imported successfully.
 * The certificate can be used to encrypt email. But to make decryption work, the corresponding private key need to be import to “Decryption Key” (ID=2) as well (normally, it is imported to “Authentication Key”, which has the same ID=3 as certificate).

bc. pkcs15-init --delete-objects privkey,pubkey --id 2 --store-private-key mykey.pem --auth-id 3 --verify-pin --id 2

h3. 7. Delete certificate

bc. pkcs15-init --delete-objects cert --id 3

h2. OpenSC driver details

OpenPGP Cards only implement a small subset of the ISO/IEC 7816-4/-8 standard. Most importantly, they do not use a file system to store the application specific data. Instead the data stored for the application is accessible via Data Objects (DO) only.

These DOs come in two variants:
* simple DOs that do not have a meaningful internal structure
* complex DOs that have a well-known internal structure which is encoded according to ASN.1 BER rules

In order to make OpenPGP Cards accessible for OpenSC's PKCS15 functions, the OpenPGP Card driver in OpenSC simulates a file system.

It does so by reading the well-known DOs on the card and converting them according to this logic: 
* simple DOs are treated as wEFs
* complex DOs are treated as DFs with their elements as children.
As complex DOs can also contain complex DOs as elements, this conversion is done recursively, leading to a multi-level hierarchy.

This file-system is currently read-only, hence any operation writing to the card, i.e. personalization and key generation, needs to be done via GnuPG.


h2. Examples

Here's an example of a card as seen via GnuPG:

<pre><code>$ gpg --card-edit
Application ID ...: D2760001240101010001000004D50000
Version ..........: 1.1
Manufacturer .....: PPC Card Systems
Serial number ....: 000004D5
Name of cardholder: John Doe
Language prefs ...: en
Sex ..............: male
URL of public key : [not set]
Login data .......: johndoe
Private DO 1 .....: [not set]
Private DO 2 .....: [not set]
Signature PIN ....: forced
Key attributes ...: 1024R 1024R 1024R
Max. PIN lengths .: 254 254 254
PIN retry counter : 0 0 0
Signature counter : 5
Signature key ....: 6B71 349B 27F0 370A A964  9BD4 967C B116 BDFA 3CDD
      created ....: 2010-03-07 09:17:36
Encryption key....: 3A2F 8637 C325 AAEE 18DD  88F1 AC40 47D4 2764 F212
      created ....: 2010-03-07 09:18:25
Authentication key: F49C 7334 2AEC B098 60C9  04C7 9BFC 9789 DF31 19A9
      created ....: 2010-03-07 09:18:25
General key info..:
pub  1024R/BDFA3CDD 2010-03-07 John Doe <john.doe@example.org>
sec>  1024R/BDFA3CDD  created: 2010-03-07  expires: never
                      card-no: 0001 000004D5
ssb>  1024R/DF3119A9  created: 2010-03-07  expires: never
                      card-no: 0001 000004D5
ssb>  1024R/2764F212  created: 2010-03-07  expires: never
                      card-no: 0001 000004D5
</code></pre>

In @opensc-explorer@ the very same card looks like
<pre><code>$ opensc-explorer
OpenSC Explorer version 0.12.1-svn
Using reader with a card: SCM SCR 335 [CCID Interface] (60600adc) 00 00
OpenSC [3F00]> ls
FileID  Type  Size
 004F    wEF    16
[005E]    DF     8
[0065]    DF    20
[006E]    DF   202
[0073]    DF   157
[007A]    DF     5
 5F50    wEF     0
[B600]    DF   141
[B800]    DF   141
[A400]    DF   141
 B601    wEF   142
 B801    wEF   141
 A401    wEF   142
OpenSC [3F00]> cd 0065
OpenSC [3F00/0065]> ls
FileID  Type  Size
 005B    wEF     9
 5F2D    wEF     2
 5F35    wEF     1
OpenSC [3F00/0065]> cat 005B
00000000: 44 6F 65 3C 3C 4A 6F 68 Doe<<John
OpenSC [3F00/0065]>
</code></pre>

h2. Tips

h3. General

 * Minimum PIN length is 6 (you get a generic "Bad PIN" error if trying to use a shorter one) and Admin PIN must be at least 8 digits.
 * OpenPGP v2.0 card can be erased with the following command (ATTENTION! ONLY USE IT ON A V2 CARD LIKE CRYPTOSTICK! WILL BRICK OTHERS!)
 <pre><code>$ opensc-tool -s 00:20:00:81:08:40:40:40:40:40:40:40:40 \
-s 00:20:00:81:08:40:40:40:40:40:40:40:40 \
-s 00:20:00:81:08:40:40:40:40:40:40:40:40 \
-s 00:20:00:81:08:40:40:40:40:40:40:40:40 \
-s 00:20:00:83:08:40:40:40:40:40:40:40:40 \
-s 00:20:00:83:08:40:40:40:40:40:40:40:40 \
-s 00:20:00:83:08:40:40:40:40:40:40:40:40 \
-s 00:20:00:83:08:40:40:40:40:40:40:40:40 \
-s 00:e6:00:00 \
-s 00:44:00:00
 </code></pre>

h3. Mac OS X

 * Use "http://www.gpgtools.org/":http://www.gpgtools.org/ to get GnuPG2 for Mac OS X
 * Remove OpenSC.tokend from !/System/Library/Security/tokend when personalizing your token. scdaemon requires exclusive access which can not be shared with OpenSC.tokend, which is started when OpenPGP Card/token is inserted.
 * kill scdaemon and re-insert your reader if you still see this:
 <pre><code>gpg: selecting openpgp failed: Card error
gpg: OpenPGP card not available: Card error
</code></pre>

h3. Linux (and Gnome)

h4. GnomeKeyring @gpg-agent@ confusion

Under Gnome, @gnome-keyring@ sets up @GPG_AGENT_INFO@:
<pre><code>$ env | grep GPG_AGENT
GPG_AGENT_INFO=/tmp/keyring-cKD5KN/gpg:0:1
</code></pre>
This agent is not capable of talking to smart cards (@--card-status@ & @--card-edit@):
<pre><code>$ gpg2 --card-status
gpg: selecting openpgp failed: Unsupported certificate
gpg: OpenPGP card not available: Unsupported certificate
</code></pre>
Solution: use @gpg2@ from a console or unset @GPG_AGENT_INFO@ to use smart card related functions:
<pre><code>$ GPG_AGENT_INFO= gpg2 --card-status
scdaemon[11344]: enabled debug flags: command cardio
Application ID ...: D2760001240102000005000005460000
Version ..........: 2.0
Manufacturer .....: ZeitControl
...
</code></pre>
Or permanently disable the @gnome-keyring@ agent:

bc. $ gnome-session-properties

And then uncheck _GPG Password Agent_, log out and log back in.

If there is no _GPG Password Agent_ entry in @gnome-session-properties@, you can put this line to _~/.bashrc_ file:

bc. unset GPG_AGENT_INFO

h4. SSH agent failure

When using OpenSSH with support of a pkcs11 module, you may fail:

bc. $ ssh-add -s /usr/lib/opensc-pkcs11.so
Enter passphrase for PKCS#11: 
SSH_AGENT_FAILURE
Could not add card: /usr/lib/opensc-pkcs11.so

"Solution":https://bugs.launchpad.net/ubuntu/+source/openssh/+bug/791747

h4. @gpg2@ and multiple readers

@gpg2@ only works if the OpenPGP compatible card is in the first listed reader:

<pre><code>$ opensc-tool -l
# Detected readers (pcsc)
Nr.  Card  Features  Name
0    No              Sitecom USB simcard reader MD-010 00 00
1    Yes             German Privacy Foundation Crypto Stick v1.2 01 00
$ GPG_AGENT_INFO= gpg2 --card-status
scdaemon[10980]: enabled debug flags: command cardio
gpg: selecting openpgp failed: Card not present
gpg: OpenPGP card not available: Card not present
</code></pre>

versus

<pre><code>$ opensc-tool -l
# Detected readers (pcsc)
Nr.  Card  Features  Name
0    Yes             German Privacy Foundation Crypto Stick v1.2 00 00
1    No              Sitecom USB simcard reader MD-010 01 00
$ GPG_AGENT_INFO= gpg2 --card-status
scdaemon[11344]: enabled debug flags: command cardio
Application ID ...: D2760001240102000005000005460000
Version ..........: 2.0
Manufacturer .....: ZeitControl
...
</code></pre>
Solution: remove other smart card readers. If all readers are USB, killing pcscd and inserting readers in the "right order" (Nitrokey first) helps. If this is not possible (for example, a reader integrated into the keyboard) editing the "CCID driver":http://pcsclite.alioth.debian.org/ccid.html Info.plist file and removing entries related to the "other" smart card readers can help.
Alternatively look up the name of the reader you are using and add it to _~/.gnupg/scdaemon.conf_ (or @--reader-port@ command line option if using GnuPG 1.X):
 <pre><code> reader-port "German Privacy Foundation Crypto Stick v1.2 01 00"</code></pre>
After this a reader other than the first reader can be used. Be sure to change the configuration file if your reader setup changes (like more readers are added before the right one) as the numbering at the end of the name changes.

h4. No readers error

If there are no readers connected, @gpg2@ gives a "generic" error message:
<pre><code>$ opensc-tool -l
No smart card readers found.
$ GPG_AGENT_INFO= gpg2 --card-status
scdaemon[11033]: enabled debug flags: command cardio
gpg: selecting openpgp failed: Card error
gpg: OpenPGP card not available: Card error
</code></pre>
This is just for your information.

h2. Links

 * "http://web.monkeysphere.info/":http://web.monkeysphere.info/
