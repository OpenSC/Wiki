h1. Using smart cards with applications

This is an incomplete list of (mostly open source) <b>end-user applications</b> that are capable of working with smart cards initialized and/or supported by OpenSC, grouped by function. Software development libraries and helpers are listed on [[DeveloperInformation|Creating-applications-with-smart-card-support]] page. 

h2. Connection authentication + encryption


h3. Web browsers / HTTPS

 * "Mozilla Firefox":http://www.mozilla.com/en-US/firefox/firefox.html - See [[MozillaSteps|Installing-OpenSC-PKCS#11-Module-in-Firefox,-Step-by-Step]] for instructions
 * "Safari":http://www.apple.com/safari/ (on Mac OS X) - requires [OpenSC Mac OS X installer] and works transparently

h3. SSH

 * See [[SSH Secure Shell]] for instructions on how to use OpenSSH or Putty

h3. VPN

 * "OpenConnect":http://www.infradead.org/openconnect/ (client for Cisco AnyConnect SSL VPN) supports PKCS#11 for client authentication. "HOWTO":http://www.gooze.eu/forums/support/howto-connect-to-cisco-anyconnect-vpn-using-openconnect-and-pki-token
 * "OpenVPN":http://www.openvpn.net (SSL VPN) supports PKCS#11 for client authentication. "Documentation":http://openvpn.net/index.php/open-source/documentation/howto.html#pkcs11
 * "strongSwan":http://www.strongswan.org/ (IPSec VPN) supports PKCS#11 modules for RSA keys so it can be used with OpenSC. "Documentation":http://wiki.strongswan.org/projects/strongswan/wiki/SmartCards and "installation instructions":http://www.strongswan.org/docs/install.htm#chapter_3.3. StrongSwan has limitations in PKCS#11 slot ID length, see "this post":http://www.opensc-project.org/pipermail/opensc-devel/2010-April/013983.html on opensc-devel for more information.
 * "Openswan":http://www.openswan.org/ 2.4.X includes code to link directly against libopensc, this has been deprecated with OpenSC versions from 0.12 onwards. "README.x509":http://www.openswan.org/docs/local/README.x509 has a chapter 8  about smart card support. Openswan 2.6.X seem to have PKCS#11 support but there is no visible documentation. 

h3. Misc

 * [[WiFi WPA authentication|Wireless-authentication]]

h2. Data signing + encryption


h3. E-mail / S/MIME

 * "Mozilla Thunderbird":http://www.mozillamessaging.com/en-US/thunderbird/ and derivates (like "Trustedbird":http://www.trustedbird.org/tb/Main_Page) - see [[MozillaSteps|Installing-OpenSC-PKCS#11-Module-in-Firefox,-Step-by-Step]] for instructions
 * "Evolution":http://projects.gnome.org/evolution/index.shtml - see [[EvolutionSteps|Using-OpenSC-in-Evolution]] for instructions

h3. Application specific document signing

 * "OpenOffice":http://www.openoffice.org/ internal "digital signatures":http://wiki.services.openoffice.org/wiki/Digital_Signatures
  * Built-in support in OpenOffice.org  "http://wiki.services.openoffice.org/wiki/How_to_use_digital_Signatures":http://wiki.services.openoffice.org/wiki/How_to_use_digital_Signatures
 * "PDF":http://www.adobe.com/security/digsig.html
  * "Sinadura":http://www.sinadura.net/inicio - a multiplatform PDF signing application with PKCS#11 support. Source code "available under GPL":http://floss.esle.eu/projects/sinadura/. Mostly targeting Spanish speaking people.
  * "OpenSignature":http://opensignature.sourceforge.net/english.php - a multiplatform PDF signing application with smart card support. Source code is available under GPL. Mostly targets Italian speaking people.
  * "jPdfSign":http://private.sit.fraunhofer.de/~stotz/software/jpdfsign - a commandline application written in Java which allows to add an invisible signature to PDF documents. The private key for signing the PDF document have to be stored in an password protected PKCS#12 file or in a PKCS#11 compatible hardware-tokens.
 * Generic
  * "Cryptonit":http://sourceforge.net/projects/cryptonit/ is a multiplatform open source (GPL) signing and (de)crypting software with PKCS#11 support that generates PKCS#7 containers.

h3. Legally binding (non-repudiation) signature software

 * DigiDocClient3 (also known as qdigidoc) implements DigiDoc/BDOC format (a "XAdES":http://uri.etsi.org/01903/v1.1.1/ profile) and available as LGPL "source":https://id.eesti.ee/idtrac/browser/qdigidoc or [ftp://ftp.id.eesti.ee/pub/id/macosx/ multiplatform binary] from "https://id.eesti.ee/idtrac/wiki/ArendajaSissejuhatus.":https://id.eesti.ee/idtrac/wiki/ArendajaSissejuhatus. This obsoletes gdigidoc.
  * DigiDoc is the official legally binding signature format used in Estonia (and Latvia and Lithuania) See "http://wpki.eu":http://wpki.eu for more information
  * Companion utility, DigiDoc3Crypto provides encryption functionality.
 * "j4sign (freesign)":http://j4sign.sourceforge.net/ is a multiplatform open source legal signature software with PKCS#11 support. Currently in Italian.

h2. Local authentication / login

 * Linux/"PAM":http://www.kernel.org/pub/linux/libs/pam/
  * "pam_pkcs11":https://github.com/OpenSC/pam_pkcs11/wiki - feature-ritch PAM module, supporting LDAP, OCSP, X509 checks.  
   * "Tutorial on pam_pkcs11 and pam_krb5":https://blog.ryandlane.com/2008/10/21/seamless-smartcard-login-with-pam_pkcs11-and-pam_krb5-against-an-active-directory-domain-using-red-hat-enterprise-linux-5-part-1/
  * [[pam_p11|pam_p11-simple-RSA-authentication-with-PKCS#11-modules]] - a simple PAM module for RSA public key authentication.
 * Mac OS X
  * Possible, but complicated and fragile. Documentation available for "10.4":http://www.opensc-project.org/sca/wiki/LogonAuthenticate and [10.5+]

h2. Disk encryption

 * "TrueCrypt":http://www.truecrypt.org/ can use PKCS#11 tokens as keyfile stores. NB! TrueCrypt does not use asymmetric keys generated on the card but stores symmetric keys as data files in the token! This requires write access to the token and keyfiles are extracted in plaintext on every use.
 * "Linux disk encryption":http://wiki.tuxonice.net/EncryptedSwapAndRoot

h2. Miscellaneous applications

 * "GnuPG":http://www.gnupg.org/ can be configured to work with whatever smart card that provides a PKCS#11 library. See "gnupg-pkcs11":http://sites.google.com/site/alonbarlev/gnupg-pkcs11 for more information. Be aware - configuring and using this solution is not trivial.
 * [[HBCI Home banking|HBCI-homebanking]]

h2. PKI/CA

 * "EJBCA":http://ejbca.org is a complete open source J2EE implementation of CA and RA software. It supports PKCS#11 for CA key storage. Compatibility with issuing OpenSC created smart cards for end users has been tested. Using OpenSC cards to store CA keys are yet to be tested.  
 * "OpenCA":http://www.openca.org/openca/ is an open source CA offering PKI services. It includes code to use the command line tools of OpenSC in a scripted way, no PKCS#11 support.
 * "XCA":http://xca.hohnstaedt.de/ is an open source CA GUI using OpenSSL and QT4. It supports PKCS#11 to manage and use keys and certificates on smart cards.
 * "step-ca":https://smallstep.com/docs/step-ca is an open-source, online CA written in Go. It supports PKCS#11 for certificate signing operations on HSMs.

h2. Work in progress

The following projects are working on adding PKCS#11 support into their software. People who feel comfortable working with source code can check out the latest snapshots.

h3. CA

 * "gnoMint":http://gnomint.sourceforge.net is an X.509 Certification Authority management tool. Currently, it has two different interfaces: one for GTK/Gnome environments, and another one for command-line. Windows port soon (patch submitted). Import/Export to pkcs12 format. Will soon include some OpenSC support.
