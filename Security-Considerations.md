# Security Considerations

For most users and uses, OpenSC should work reasonably securely out of the box, with the assumption that the software and physical environment of the user has not been already compromised. While smart cards are designed to protect sensitive key material in hostile environments, there are many attacks that can be launched against a system utilizing OpenSC software. If you're serious about security or have healthy paranoia, this page contains tips and notes to push the limits even further.

## The basics

To keep your private keys secret and to prevent unauthorized use of them:

* Securely initialize your card. Key generation and distribution are the most vulnerable parts of PKI systems.
* Cards should be personalized on a computer disconnected from the internet, on a clean and verified system. A Linux live CD works well. Under Debian, you may also use a [read-only root system](http://wiki.debian.org/ReadonlyRoot).
* Always generate keys on the card (vs importing a key), unless you know what and why you are doing. This guarantees a single copy of the private key which is safely stored inside the smart card.
* Always use a pinpad reader, this includes personalization. Even a computer disconnected from the internet and running a fresh Linux live CD does not rule out the possibility that an adversary has installed a hardware keylogger to your computer.
* Use secure PIN codes. Even if smart cards usually lock up after 3 incorrect PIN tries, a longer PIN code makes it harder to guess or sniff or overlook.
* Choose safe key sizes. The 1024 bit RSA keys are supposed to be [phased out](http://www.keylength.com/en/) by now and 2048 or longer keys used. Most modern smart cards support 2048 and more bit RSA keys.
* Securely use your card.
* Don't use your smart card in a system you can not trust. A pinpad reader does not help if a trojan tricks you into entering the PIN.
* Keep your system up to date, this includes system updates, application updates, antivirus updates.
* Use a pinpad reader. Without it the PIN code travels in plaintext through the whole computer and malware can easily sniff and forward the PIN code to the adversary or use a cold boot attack to recover the PIN code.
* Remove your card from the reader as soon as you have finished usinsoftg it.

## Relevant `opensc.conf` file entries

> OpenSC will use the file in `OPENSC_CONF` environment variable if present!

## `debug` / `debug_file`

**Implications:**

* By default OpenSC does not write a debug log (`debug = 0;` in `opensc.conf`) but if instructed to do so (by setting `debug` to a number `>0`, via `OPENSC_DEBUG` environment variable or `-v`/`-verbose` command line option), **all** APDU communication is written to disk (to the file specified with `debug_file` in `opensc.conf`).
  * This can include sensitive information such as PIN codes (if a pinpad reader is not used) or private key material (if a key is imported to the card and not generated onboard).

**Actions:**

* Don't use debugging in a production environment if at all possible
* Don't have a debug_file configuration in opensc.conf, even if `debug` is set to `0` in the configuration file - it can be overridden via the environment variable or command line options and sensitive data can be written to the disk.
* When sending the debug log to the list for help, either use throwaway keys and dummy PIN codes (1234, 0000 etc) or remove sensitive parts from the log. Developers prefer full logs with throwaway content for their completeness.

## `profile_dir`

**Implications:**

* If OpenSC is used to personalize smart cards and the adversary can modify the profile of the card, ACL-s written to the card can be altered and result in world-readable private keys instead of PIN protected keys.

**Actions:**

* Make sure that profile files can not be altered by non-administrator users.
* If needed, manually verify the contents of profile files before personalization.

### `connect_exclusive`

**Implications:**

* Normally OpenSC shares smart card readers (and cards) with other applications, meaning it does not specifically block other applications from accessing them. If such behavior is not desired, turning this option on makes OpenSC connect to readers in exclusive mode, so that no other application could connect to the reader

### `enable_pinpad`

**Implications:**

* OpenSC by default detects pinpad readers and supports using them with compliant applications. Turning this option off disables the pinpad and PIN codes can be sniffed by malware or keyloggers.
* Even if pinpad is enabled in OpenSC, a badly written application might ignore it and pop up a window asking for the PIN code and trick the user into entering his/her PIN.

**Actions:**

* If present, always use the pinpad exclusively.

### `provider_library`

**Implications:**

* OpenSC loads the PC/SC implementation dynamically. If the adversary can change the library path or trick OpenSC into loading a crafted implementation, the adversary can sniff or alter all APDU communication to the smart card.

**Actions:**

* Always use a pinpad.

### `use_pin_caching`

**Implications:**

* For usability reasons (to limit the number of times the user would have to enter the PIN) OpenSC caches PIN codes, which don't require user consent (a PIN entry before each operation with a key). This only makes sense without a pinpad. A PIN code is "recycled" `pin_cache_counter` times (default is 3 times) after what re-validation will fail and application should again prompt the user for the PIN.

### `use_file_caching`

**Implications:**

* To speed up operation, OpenSC can store certificates and files on hard disk. If the adversary can tamper with the file based cache, certificates can be swapped against fake ones.

### lock_login

**Implications:**

* By default OpenSC PKCS#11 module keeps the card available to other applications and only locks the card for the duration of actual communication with the card. If keys on the card are left in authorized state, another application could misuse the keys. To prevent this, `lock_login` can be enabled which will lock the card for a single application after successfully calling `C_Login`.
* Many applications (like Firefox, Thunderbird) call C_Logout only when they are closed, so with this option enabled only a single application has access to the card simultaneously.

## Generic issues

### Publicly readable information

**Implications:**

* If the card contains sensitive/private information (for example, a certificate with name and private  e-mail address) and the card is lost, adversary can use that information without knowing the PIN of the card.

**Actions:**

* Protect reading certificates with a PIN code.
* Make sure to not put any sensitive information into PKCS#15 labels.

## Multiuser implications

### Command line arguments

The OpenSC tools allow you to specify PINs and keys on the command line. This is only suitable for testing or when you are the only user of the machine. If there are multiple users, other users usually are able to run things like `ps` or `top`, and probably are able to see the arguments given to some process, too. Also, the arguments probably get logged to some shell history file like `~/.bash_history`.

The solution is to use a script or, in the case of the `pkcs15-init` tool to put PINS and keys into a file and used through the `-options-file` options.

## Access to the card

Some other problems if multiple users have access to the reader(s):

If the user forgets a card to the reader while the session isn't locked, a malicious other user could run PIN verify commands to the card and probably lock the PIN, or even lock the card for good. If a user is logged in to the card but the session isn't locked, a malicious user could use the privileged functionality (e.g. doing a signature, writing data to the card).

A solution is to add the user to a specific "scard" group after they've logged in through xdm. The pcsc-lite's pcscd runs as pseudouser/group scard/scard, and limit the access to the server socket (pcscd.comm) as 770 scard:scard. This way, other possible users that may have logged in through ssh won't have any access to the local card readers. Not a perfect solution, but works for single-reader workstations well enough.

By default OpenSC allows multiple applications to access smart cards via the PKCS#11 module. If you don't want that, you can set `lock_login = true;` in `opensc.conf` file and the card will be locked as soon as application calls `C_Login` until `C_Logout` is called. Several applications (like Firefox, Thunderbird) won't call `C_Login` before the application is closed, so you can only use a single application at a time.

## Protection of cards made with the `pkcs15-init` tool

Most cards have a default transport key that is used to create a pkcs15 directory on the card. Within the pkcs15 directory, files and keys are protected by PINs so the transport key has no power there.

This means that your keys and sensitive data are safe against others (who know the default transport key), in the sense that they can't be read or used.

However, depending on the smartcard os and the card profile anyone who knows the transport key and has access to your card can erase the card.

On itself, that may be a good thing if you lost your card, but there's another problem: If your card contains trusted certificates, and an adversary steals your card, puts another pkcs15 dir with other certs on the card and puts it back without you knowing, you may not find out until you put trust in those untrusted certs. 

Be very careful when using the card as a tamper-resistant storage - make them PIN-protected for example.
(Note: this if often not the case: the trusted certificates are usually stored in the applications using them.)

## Root access

From the above, it follows that you can't protect your card, nor use your card to protect something against someone with root access or who can change the config/profile files, binaries or sniff/modify the communication with the card.
