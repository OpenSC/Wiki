# Aventra MyEID PKI Smart Card

Aventra MyEID PKI Smart Card is a cryptographic smart card conforming to common Public Key Infrastructure standards like ISO7816 and PKCS#15.
It can be used for various tasks requiring strong cryptography, e. g. logging securely to Windows, encrypting e-mail, authentication, and electronic signatures. The card is also available as a Dual Interface version, compatible with T=CL protocol and also emulating Mifare™ DESFire. The card is based on Java Card technology, with Aventra MyEID applet that implements the command interface and file system, and the security microcontroller which provides the cryptographic functionality.

The card material is PVC as standard, making it suitable for visual personalization using thermal transfer or dye sublimation printers. Customer specific layouts can be delivered in offset and silk screen printing. Optional features include signature panel, holograms, security printing etc. A SIM-sized version is available.

The cards can be personalized both visually and electrically by Aventra according to customer specifications, or the customers can personalize the cards themselves using Active Process Manager developed by Aventra, or software from other parties.

Aventra participates in development and testing of OpenSC, keeping MyEID support up to date with new MyEID versions.

> In addition to OpenSC, Aventra provides a propitery MyEID minidriver that is certified by Microsoft and supports Smart Card Plug and Play.

## Aventra MyEID PKI applet

The MyEID applet implements all the basic functionality of a Public Key Infrastructure (PKI) token specified in the most common international PKI standards, such as ISO 7816-15. MyEID supports authentication with alphanumeric and challenge/response PIN codes. It can emulate a PIV/CIV card by mapping the ISO 7816-15 (PKCS#15) structure to the PIV/CIV command interface.

> Aventra’s MyEID PKI Smart Card has evolved into version 5, based on NXP’s SmartMX3 microcontroller and JCOP 4 Java Card platform. 
MyEID 5.0.0 is certified to Common Criteria EAL4+ level.
### New in MyEID 5

EEPROM storage space is increased to 180 kilobytes. New features include:
* Secure Messaging as specified in [NIST SP 800-73-5](https://csrc.nist.gov/pubs/sp/800/73/pt2/5/final)
* Extended length APDUs
* Challenge/response PINs with AES algorithm
* Brainpool ECC curves
* on-card PSS and OAEP padding


### Technical details

#### Platform

* Since MyEID 5: JavaCard™ 3.0.5 with Global Platform 2.3

#### Supported standards and specifications

* ISO/IEC 7816-4 to 7816-9, 7816-15
* ISO/IEC 14443 T=CL, Mifare™ DESFire EV2/EV3 interface available as an option
* PKCS#7 and PKCS#15
* FINEID S4-1 and S4-2
* PIV

#### Common features

* 512 - 4096 bit RSA cryptographic operations with on card key generation
* 192 - 521 bit ECC operations with on card key generation
* Secure random number generator (FIPS 140-2)
* symmetric encryption algorithms with AES algorithm and 128, 192 and 256 bits key lengths.
* SHA-256, SHA-1 and MD5 one way hash algorithms
* Since MyEID 4: ECDSA and ECDH operations
  
#### Other features

* 180K EEPROM memory Dual Interface version supports ISO/IEC 14443 T=CL and optionally Mifare™ DESFire EV2.
  
#### Compatible software

* OpenSC
* Aventra MyEID Minidriver for Windows
* Fujitsu mPollux DigiSign™ middleware
* Versasec vSEC:CMS
* Citrix™
* Cisco VPN Client
* Large number of software products that support Microsoft™ CryptoAPI, Microsoft Cryptography API: Next Generation (CNG) or PKCS#11 Token Interface

## OpenSC support

OpenSC 0.11.4 was the first version that had support for the MyEID card. At that time the patch required was provided by Aventra when requested. Since the version 0.11.10 support for the MyEID card is included to the official release. OpenSC initialization is supported from version 0.12.

MyEID supports 512 bit to 4096 bit RSA keys and EC keys in OpenSC.

### Initialization

Cards can be initialized with OpenSC. The `myeid.profile` file defines the cards structure and access conditions. After initialization the card should be finalized to activate the card (PINs).

The initialization does not create the User PIN (PIN 1). This is done separately. During initialization OpenSC will ask for the Security Officer PIN and PUK and will also create it (can also be specified  as parameters with the options `--so-pin` and `--so-puk`).

> When initializing cards, specify the PIN and PUK (with `--pin` and `--puk` parameters) to prevent OpenSC from unnecessarily asking for it several times. You can use any values, because the PIN is not created here.

```bash
pkcs15-init -C --pin 1111 --puk 1111 --so-pin 12345678 --so-puk 12345678 
```

PINs are created in the following way (add at least PIN nbr 1 (User PIN), the SO-PIN was created in the previous step). OpenSC will ask for PIN and related PUK if not specified as parameters. The card supports up to 14 PINs.

```bash
pkcs15-init -P -a 1 -l "Basic PIN"
pkcs15-init -P -a 2 -l "Sign PIN"
```

Write the certificate and key to the card

```bash
pkcs15-init --store-private-key key.pem --auth-id 01 --id 11 --so-pin 12345678 --pin 1111
pkcs15-init --store-certificate cert.pem --auth-id 01 --id 11 --format pem --pin 1111
```

The keys can be also generated securely directly on the card.

```bash
pkcs15-init --generate-key rsa:2048 --auth-id 01 --so-pin 12345678 --pin 1111
pkcs15-init --generate-key ec:prime256v1 --auth-id 01 --so-pin 12345678 --pin 1111

```

When done creating PIN codes, finalize (activate) the card. After this all access conditions (PINs) are in effect. This is not mandatory, but before this is done card elements can be accessed without satisfying specified access conditions (without entering PIN codes). 

```bash
pkcs15-init -F
```
**NOTE:** Since MyEID 5, cryptographic operations such as signature creation are not allowed before the card is finalized. This is to prevent accidentally forgetting the card in unfinalized state. Crypto-operations can be explicitly enabled in creation (unfinalized) state, see the reference manual for details.

### Smart card reader configuration

MyEID card uses T=1 protocol. This basically means that the response data is sent with the answer to the command/request. With T=0 protocol the smart card will first answer to the command and tell how much data it will send. Data is then requested separately.

In some environments there has been issues when reading files that exceed some threshold. If you encounter problems when reading larger files from the card (e.g. certificates) with no apparent reason, try to set the readers `max_recv_size` (max receive size) to e.g. 192, to be on the safe side. You can then try to iterate to find the maximum for your environment.

The setting in the `opensc.conf` (usually in `/etc` or `/etc/opensc`) config file is the following:

```text
...
	reader_driver pcsc {
		# This sets the maximum send and receive sizes.
		# Some reader drivers have limitations, so you need
		# to set these values. For usb devices check the
		# properties with lsusb -vv for dwMaxIFSD
		#
		# max_send_size = 254;
		# max_recv_size = 254;
		max_recv_size = 192;
...		
	}

	reader_driver openct {
...
		# max_send_size = 252;
		# max_recv_size = 252;
		max_recv_size = 192;
...
	};
```

## Links & other information

Card details can found in [Reference manual](https://aventra.fi/wp-content/uploads/2026/03/MyEID-PKI-Smart-Card-Reference-Manual-3-0-8-signed.pdf).

Cards can be bought from Aventra as blank cards or according to customer specifications regarding appearance etc. Small quantities of cards and readers can be easily bought from the [web shop](https://shop.aventra.fi/). For larger quantities contact Aventra sales for a quote.  

* [Aventra website](https://aventra.fi/)
* [Web shop](https://shop.aventra.fi/)
* [Downloads](https://aventra.fi/downloads/)

### About Aventra

Aventra is a high tech company specializing in information security products and services. We are especially focusing on Public Key Infrastructure technologies. Most of our products are developed in house.

Aventra offers a complete portfolio of card products ranging from simple plastic cards to high security smart cards and tokens. Our most recent product line features security solutions for mobile applications.  We also provide complete services and systems for issuing and managing cards and secure tokens, including card printers and materials.

## Notes

* Card requires a PUK code when creating a PIN code (fails to create a PIN without a PUK).
* A minidriver is available for download [here](https://aventra.fi/downloads/).
* You can **not** upload custom Java-Applets like the openpgpcard-applet to the Aventra MyEID-card because the card is locked and Aventra refuses to hand out the required PIN. Please contact Aventra if you have any special needs or requests.
