# Using smart cards with applications

This is an incomplete list of (mostly open source) **end-user applications** that are capable of working with smart cards initialized and/or supported by OpenSC, grouped by function. Software development libraries and helpers are listed on [DeveloperInformation](Creating-applications-with-smart-card-support) page.

## Connection authentication + encryption

### Web browsers / HTTPS

* [Mozilla Firefox](https://www.mozilla.org/en-US/firefox/new/) - See [MozillaSteps](Installing-OpenSC-PKCS11-Module-in-Firefox,-Step-by-Step) for instructions
* [Safari](https://www.apple.com/safari/) (on Mac OS X) - requires [OpenSC Mac OS X installer] and works transparently

### SSH

* See [SSH Secure Shell](SSH-Secure-Shell) for instructions on how to use OpenSSH or Putty

### VPN

* [OpenConnect](https://www.infradead.org/openconnect/) (client for Cisco AnyConnect SSL VPN) supports PKCS#11 for client authentication.
* [OpenVPN](https://openvpn.net/) (SSL VPN) supports PKCS#11 for client authentication. [Documentation](https://openvpn.net/community-resources/how-to/#pkcs11)
* [strongSwan](https://www.strongswan.org/) (IPSec VPN) supports PKCS#11 modules for RSA keys so it can be used with OpenSC. [Documentation](https://docs.strongswan.org/docs/5.9/howtos/smartcards.html) and [installation instructions](https://docs.strongswan.org/docs/5.9/install/install.html).
* [Openswan](https://openswan.org/) 2.4.X includes code to link directly against libopensc, this has been deprecated with OpenSC versions from 0.12 onwards.

### Misc

* [WiFi WPA authentication](Wireless-authentication)

## Data signing + encryption

### E-mail / S/MIME

* [Thunderbird](https://www.thunderbird.net/en-US/) and derivates - see [MozillaSteps](Installing-OpenSC-PKCS11-Module-in-Firefox,-Step-by-Step) for instructions
* [Evolution](https://gitlab.gnome.org/GNOME/evolution/-/wikis/home) - see [Evolutio nSteps](Using-OpenSC-in-Evolution) for instructions

### Application specific document signing

* [OpenOffice](http://www.openoffice.org/) internal [digital signatures](https://wiki.openoffice.org/wiki/Digital_Signatures)
* Built-in support in OpenOffice.org <https://wiki.openoffice.org/wiki/How_to_use_digital_Signatures>
* [PDF](https://helpx.adobe.com/acrobat/kb/certificate-signatures.html)
* Sinadura - a multiplatform PDF signing application with PKCS#11 support. Mostly targeting Spanish speaking people.
* OpenSignature - a multiplatform PDF signing application with smart card support. Source code is available under GPL. Mostly targets Italian speaking people.
* jPdfSign - a commandline application written in Java which allows to add an invisible signature to PDF documents. The private key for signing the PDF document have to be stored in an password protected PKCS#12 file or in a PKCS#11 compatible hardware-tokens.
* Generic
* [Cryptonit](https://sourceforge.net/projects/cryptonit/) is a multiplatform open source (GPL) signing and (de)crypting software with PKCS#11 support that generates PKCS#7 containers.

### Legally binding (non-repudiation) signature software

* [DigiDoc4](http://github.com/open-eid/DigiDoc4-Client) (also known as qdigidoc/qdigidoc4) implements ASiC-S/E format (a [XAdES](https://uri.etsi.org/01903/v1.3.2/) profile) and some legacy formats.
  * DigiDoc4 is the official legally binding signature format used in Estonia (and Latvia and Lithuania).
  * Provides encryption/decryption functionality.
* [j4sign (freesign)](https://j4sign.sourceforge.io/) is a multiplatform open source legal signature software with PKCS#11 support. Currently in Italian.

## Local authentication / login

* Linux/[PAM](https://mirrors.edge.kernel.org/pub/linux/libs/pam/)
  * [pam_pkcs11](https://github.com/OpenSC/pam_pkcs11) - feature-ritch PAM module, supporting LDAP, OCSP, X509 checks.  

## Disk encryption

* [TrueCrypt](https://truecrypt.sourceforge.net/) can use PKCS#11 tokens as keyfile stores. NB! TrueCrypt does not use asymmetric keys generated on the card but stores symmetric keys as data files in the token! This requires write access to the token and keyfiles are extracted in plaintext on every use.

## Miscellaneous applications

* [GnuPG](https://www.gnupg.org/) can be configured to work with whatever smart card that provides a PKCS#11 library. See gnupg-pkcs11 for more information. Be aware - configuring and using this solution is not trivial.

## PKI/CA

* [EJBCA](https://www.ejbca.org/) is a complete open source J2EE implementation of CA and RA software. It supports PKCS#11 for CA key storage. Compatibility with issuing OpenSC created smart cards for end users has been tested. Using OpenSC cards to store CA keys are yet to be tested.  
* OpenCA is an open source CA offering PKI services. It includes code to use the command line tools of OpenSC in a scripted way, no PKCS#11 support.
* XCA is an open source CA GUI using OpenSSL and QT4. It supports PKCS#11 to manage and use keys and certificates on smart cards.
* [step-ca](https://smallstep.com/docs/step-ca/index.html) is an open-source, online CA written in Go. It supports PKCS#11 for certificate signing operations on HSMs.

## Work in progress

The following projects are working on adding PKCS#11 support into their software. People who feel comfortable working with source code can check out the latest snapshots.

### CA

* [gnoMint](https://sourceforge.net/projects/gnomint/) is an X.509 Certification Authority management tool. Currently, it has two different interfaces: one for GTK/Gnome environments, and another one for command-line. Windows port soon (patch submitted). Import/Export to pkcs12 format. Will soon include some OpenSC support.
