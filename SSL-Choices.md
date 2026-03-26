# SSL Choices

If you want to write an SSL enabled application and use smart cards for client authentication,
you need a library that offers this. Here we list SSL libraries we know and whether they work
with OpenSC.

We aim to provide example code, but so far there is none.

## Windows

If you plan a windows only application and want to develop with Visual C/C++/C#/.Net you can use those. As far as we know you don't need to do anything special to enable using smart card in your application, as the [Crypto API](https://learn.microsoft.com/en-us/windows/win32/seccrypto/cryptoapi-system-architecture) and a [CSP module](https://learn.microsoft.com/en-us/windows/win32/seccrypto/csps-and-the-cryptography-process) will take care of everything.

## Mac OS X

In theory the same situation (use Mac OS X developer tools, use the Apple [CDSA](https://developer.apple.com/library/archive/documentation/Security/Conceptual/cryptoservices/CDSA/CDSA.html)/CSP API).

In practice there is no bridge between OpenSC and the Apple CDSA/CSP API, so currently you won't be able to use OpenSC. But work is in progress, see [OpenscTokend](OpenSC.tokend).

## Linux

There are many different crypto libraries such as OpenSSL, GnuTLS, LibNSS, cryptlib, QCA and others. We will try to discuss each.

[OpenSSL](http://www.openssl.org/) has an easy way to integrate smart card support.

The OpenSSL 3 supports a plugin-like  provider API, which is implemented by the
[pkcs11-provider](https://github.com/openssl-projects/pkcs11-provider). Upon configuring the provider in
OpenSSL, applications can use the OpenSSL STORE API to list objects on token using PKCS#11 URIs.

Our sister project
[libp11](https://github.com/OpenSC/libp11) has code to make using OpenSC PKCS#11 module with OpenSSL quite easy and should include example code for using SSL with client certificate authentication using a smart card soo. Also the engine_pkcs11 project has a so called engine so you can change any code using OpenSSL to move the crypto operation from your CPU to your smart card with only a few small changes. `Wpa_supplicant` is an example of an application using OpenSSL and this engine for smart card support.

[GnuTLS](https://www.gnutls.org/) since version 2.12.0 can redirect cryptographic operations to a [PKCS 11 module](https://www.gnutls.org/manual/html_node/Smart-cards-and-HSMs.html). Smart cards can be enabled by using the `opensc-pkcs11` module.

[NSS](http://www.mozilla.org/projects/security/pki/nss/) is the netscape security layer used in applications like Mozilla, Firefox and Thunderbird. It includes support for using PKCS#11 modules like the OpenSC PKCS#11 module, but we don't have example code how to do that right now.

[cryptlib](http://www.cs.auckland.ac.nz/~pgut001/cryptlib/) is a library by Peter Gutmann and seems to implement every crypto standard we ever heard of, including smart card support using PKCS#11 modules. However we are not sure what the license of this library is, and we have no experience in using it or writing applications that use smart cards with it.

[QCA](https://api.kde.org/qca/html/index.html) is the Qt Cryptographic Architecture is an addon for Qt that adds crypto operations. QCA has been moved to the kdesupport part of the kde source code and will be part of the next KDE release. As far as we know some recent versions of QCA include the ability to use PKCS#11 modules such as OpenSC.
