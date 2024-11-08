# Aventra MyEID PKI card

Aventra MyEID PKI Card is a cryptographic smart card conforming to common Public Key Infrastructure standards like ISO7816 and PKCS#15.
It can be used for various tasks requiring strong cryptography, e. g. logging securely to Windows, encrypting e-mail, authentication, and electronic signatures. The card is also available as a Dual Interface version, compatible with T=CL protocol and also emulating Mifare™. The card is a JavaCard with Aventra MyEID applet that implements the functionality.

The card material is PVC as standard, making it suitable for visual personalization using thermal transfer or dye sublimation printers. Customer specific layouts can be delivered in offset and silk screen printing. Optional features include magnetic stripe, signature panel, holograms, security printing etc.

The cards can be personalized both visually and electrically by Aventra according to customer specifications, or the customers can personalize the cards themselves using ActivePerso Manager developed by Aventra, or software from other parties.

Aventra participates in development and testing of OpenSC, keeping MyEID support up to date with new MyEID versions.

> In addition to OpenSC, Aventra provides a propitery MyEID minidriver that is certified by Microsoft and supports Smart Card Plug and Play.

## Aventra MyEID PKI applet

The MyEID applet implements all the basic functionality of a Public Key Infrastructure (PKI) token specified in the most common international PKI standards, such as PKCS#15. Users optionally have a choice between different authentication methods to the token. Besides the standard PIN number, there are currently two other authentication mechanisms available. The GrIDsure® one time PIN is based on a pop-up challenge grid that is used to form a onetime PIN that cannot be used by outsiders watching the authentication. MyEID tokens are also compatible with PalmSecure™ biometric technology, which is based on the unique blood vein patterns in the palm of the user’s hand replacing the PIN.

MyEID 4 added support for Elliptic Curve Cryptography and many other new features. It can emulate a PIV/CIV card by mapping the ISO 7816-15 (PKCS#15) structure to the PIV/CIV command interface.

> Aventra’s MyEID PKI Smart Card has evolved into version 4.5, adding support for 4096 bit RSA keys, faster operation and more storage space. MyEID 4.5 is based on NXP’s SmartMX2 SECID P60 microcontroller, with JCOP3 Java Card Operating System. This microcontroller replaces NXP’s older JCOP2 based modules.

### New in MyEID 4.5

EEPROM storage space is increased to 144 kilobytes. RSA keys are supported up to 4096 bit key length. MyEID 4.5 is faster than earlier versions in performing Elliptic curve cryptography and RSA operations with keys up to 2048 bit. 4096 bit operations are fast as well, with on card key generation taking around one minute and digital signature calculation around 3000-4000 milliseconds. MyEID 4.5 contains some new features for securely transferring symmetric keys between the card and outside world (key wrapping/unwrapping).

### Technical details

#### Platform

* JavaCard™ from 2.2.1 and above, Global Platform 2.1.1
* Since MyEID 4.5: JavaCard™ 3.0.4 with Global Platform 2.2.1

#### Supported standards and specifications

* ISO/IEC 7816-4 to 7816-9, 7816-15
* ISO/IEC 14443 T=CL and Mifare™
* PKCS#7 and PKCS#15
* FINEID S4-1 and S4-2
* PIV

#### Common features

* 512 - 4096 bit RSA cryptographic operations with on card key generation
* 192 - 521 bit ECC operations with on card key generation
* Secure random number generator (FIPS 140-2)
* DES, 3DES, AES128, AES256 symmetric encryption algorithms
* SHA-256, SHA-1 and MD5 one way hash algorithms
* Since MyEID 4: ECDSA and ECDH operations
  
#### Other features

* 144K EEPROM memory Dual Interface version supports ISO/IEC 14443 T=CL and Mifare™ Flex
  
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

Card details can found in [Reference manual](https://aventra.fi/wp-content/uploads/2024/03/MyEID-PKI-JavaCard-Applet-Reference-Manual.pdf).

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
