# Creating applications with smart card support

If you, as a developer, want to write software that can work with cryptographic smart cards, you need to orientate in the maze of different APIs.

## Platform crypto interfaces

### PKCS#11

PKCS#11 is a cross-platform cryptographic interface defined by RSA Laboratories. In practice PKCS#11 is the most universal and flexible API. OpenSC and almost all hardware cryptography device vendors (smart cards, HSM-s) provide a PKCS#11 interface with their hardware. Choose PKCS#11 as your primary interface if you are writing a specialized crypto application or universality and portability are important to you. OpenSC provides a PKCS#11 module which should be usable by any software offering PKCS#11 support.

PKCS#11 also has a drawbacks. First, applications need to be manually configured to use a specific PKCS#11 module. Second, PKCS#11 only provides cryptographic primitives, it does not provide X509 and trust management features.

#### Tools and libraries

These tools and libraries help in talking to PKCS#11 modules or integrate PKCS#11 with other frameworks.

##### C

* [libp11](https://github.com/OpenSC/libp11) is a wrapper library for PKCS#11 modules which includes an OpenSSL engine for using PKCS#11 tokens
* [pkcs11-helper](https://github.com/OpenSC/pkcs11-helper) Library that simplifies the interaction with PKCS#11 providers for end-user applications using a simple API and optional OpenSSL engine
* [gp11](http://live.gnome.org/GnomeKeyring/Architecture) is a GObject based wrapper for PKCS#11, distributed with gnome-keyring.
* [PaKChoiS](http://www.manyfish.co.uk/pakchois/) aims to provide a thin wrapper over the PKCS#11 interface.
* [p11-kit](http://p11-glue.freedesktop.org/p11-kit.html) eases working with multiple PKCS#11 modules and includes support for [PKCS#11 URI scheme](http://tools.ietf.org/html/draft-pechanec-pkcs11uri-13).
* [pkcs11-provider](https://github.com/latchset/pkcs11-provider) is an Openssl 3.x provider to access Hardware or Software Tokens using the PKCS#11 Cryptographic Token Interface.

##### Python

* [PyKCS11](https://github.com/LudovicRousseau/PyKCS11) is a python wrapper (SWIG) for PKCS#11 modules.
* [python-pkcs11](https://github.com/pyauth/python-pkcs11) is a pure Python wrapper with classes and context managers for PKCS#11 modules.

##### Java

* [Using smart cards with Java SE](Using-smart-cards-with-Java-SE) wiki page contains list of PKCS#11 wrappers
* [OpenSC-Java](https://github.com/CardContact/opensc-java) is a Java PKCS#11 wrapper and JCE Provider

### CryptoAPI (Windows)

Windows applications use CryptoAPI to do native SSL connections and to use the Windows certificate store. All applications using CryptoAPI (or CryptoAPI-NG) can transparently use all smart cards that provide a full CSP or a smart card BaseCSP module. Most card vendors provide one such driver. OpenSC has a rudimentary BaseCSP module (a MiniDriver), but it is still work in progress. Use CryptoAPI if you are writing a Windows-only application or if you want a well functioning and integrated experience for your users. CryptoAPI provides trust management (user has a centralized location to set the trust bits for various X509 certificates) in addition to basic cryptographic operations and windows API provides higher level functions that are CAPI enabled.

* [Example to use OpenSC with Microsoft CNG and CryptoAPI](Example-to-use-OpenSC-with-Microsoft-CNG-and-CryptoAPI)

### CDSA/Keychain (Mac OS X)

Mac OS X implements CDSA as the cryptography API for the Mac platform (in theory it is a universal specification but in practice only implemented by Apple). OS X 10.4 until 10.11 and above implement Tokend, what is similar in nature to BaseCSP on Windows - it provides a plugin architecture to add support for smart cards into the system. Applications using CDSA can then transparently make use of smart cards. Applications usually use higher level API-s (like Keychain or SSL socket API-s) that internally make use of CDSA. Trust management is exposed centrally via Keychain.app. Use Keychain API-s if writing a native application for Mac OS X or if you want to have a smooth "Mac style" user experience.

* [Mac Documentation: Security](https://developer.apple.com/documentation/security/)

### Java Cryptography Architecture (JCA)

* [Using smart cards with Java SE](Using-smart-cards-with-Java-SE)
* Sample [applet](http://emergya.github.com/opensc-testing/) with a [JCA provider](http://docs.oracle.com/javase/1.5.0/docs/api/java/security/Provider.html) using OpenSC.
  * [source](http://github.com/emergya/opensc-testing)
  * [javadoc](http://emergya.github.com/opensc-testing/apidocs/index.html)

## SSL/TLS toolkit integration

### OpenSSL

[OpenSSL](http://www.openssl.org/) has an easy way to integrate smart card support.

The [libp11](https://github.com/OpenSC/libp11/wiki) has code to make using OpenSC PKCS#11 module with OpenSSL quite easy and includes example code for using SSL with client certificate authentication using a smart card too.
The use of engines in OpenSSL are deprecated from the version 3.

The engine_pkcs11 project has an OpenSSL engine implementation so you can change any code using OpenSSL to move the crypto operation from your CPU to your smart card with only a few small changes.
It was merged into libp11 project.

The [pkcs11-provider](https://github.com/latchset/pkcs11-provider) is an Openssl 3.x provider to access Hardware or Software Tokens using the PKCS#11 Cryptographic Token Interface. It is still work in progress.

### NSS

[NSS](http://www.mozilla.org/projects/security/pki/nss/) (Network Security Services) is used by Mozilla products (Firefox, Thunderbird) and many applications in Fedora, like [mod_nss](https://pagure.io/mod_nss). NSS supports PKCS#11 for SSL.

### QCA

[QCA](http://api.kde.org/kdesupport-api/kdesupport-apidocs/qca/html/) (Qt Cryptographic Architecture) adds cryptography support into Qt applications. QCA has PKCS#11 support since v2.0.

### GnuTLS

[GnuTLS](http://www.gnutls.org) includes native PKCS#11 smart card support using the PKCS#11 URI scheme..
See <http://www.gnutls.org/manual> for more information.

### cryptlib

[cryptlib](https://www.cs.auckland.ac.nz/~pgut001/cryptlib/) is a library by Peter Gutmann and claims support for SSL and PKCS#11 modules.

## Low level smart card access

OpenSC is for cryptographic smart cards and the preferred method for accessing such cards is via one of the high level cryptographic API-s listed above, which hides the details of actual card reader access via one of the interfaces described below. As a general rule, don't use the low level smart card API-s if the necessary functionality is implemented via a cryptographic API.

### PC/SC

PC/SC is a standard from [PC/SC Workgroup](https://pcscworkgroup.com/) but the "reference implementation" is still [Windows winscard.dll](http://msdn.microsoft.com/en-us/library/aa374731(VS.85).aspx#smart_card_functions). Linux uses the open source [pcsc-lite](https://pcsclite.apdu.fr/) package. And Mac OS X uses a fork of pcsc-lite included in the [SmartCardServices](http://smartcardservices.macosforge.org/) project.

#### Tools and libraries

* Python
  * [pyscard](https://pyscard.sourceforge.io/)
* Java
  * See [[dedicated Java page|Using-smart-cards-with-Java-SE]] about `javax.smartcardio` in Java 1.6+

### CT-API

CT-API is an API for accessing smart card readers that is mostly used in Germany. It is not suited for modern multi-user environments, is not portable and not always available. New projects should avoid using CT-API and use PC/SC instead.

### OpenCT

[OpenCT](https://github.com/OpenSC/openct), like CT-API, is a Linux only API for accessing USB tokens (and smart card readers). Very few applications beside OpenSC can make use of OpenCT readers. New projects should try to avoid building against OpenCT and use PC/SC instead.
