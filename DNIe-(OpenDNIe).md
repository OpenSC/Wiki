# DNIe (OpenDNIe)

Support for DNIe cards got merged by code from the OpenDNIe fork, by pull request #168. The code is now in Master. OpenSC does not support the Spanish Ceres cards, neither DNIe 3.0 or newer cards when used through its contactless (NFC) interface.

## DNIe background

[DNIe](https://sede.sepe.gob.es/portalSede/en/firma-electronica/DNI-electronico) is one of the SpanishEid-s. Historically, there exists a patch for OpenSC which adds support for DNIe in OpenSC.

There were two different OpenSC implementations for first-generation Spanish National eID card (DNIe / DNI 2.0) support:

* The official one provided by *Spanish Dirección General de la Policía y de la Guardia Civil (DGP)* was based in OpenSC-0.11.8, released under GPLv3. It's not being currently maintained.
* *OpenDNIe* was an alternate LGPL implementation, written from scratch based on several documents and forums around DNIe.

From the public administration point of view the card has been procured by the Ministry of Interior (DGP's DNIe office). The card chip is a ST19WL34 provided by ST Microelectronics, and its software has been procured by Fábrica Nacional De Moneda y Timbre (FNMT-RCM).

* The DNIe card software is closely related to FNMT's Ceres card, being very similar in structure and design.
* OpenDNIe is copyright 2011 of Juan Antonio Martinez ([GitHub](https://github.com/jonsito)).

In 2015, the DGP began issuing DNIe 3.0 cards. These cards feature an Infineon Technologies SLE78CLFX408AP dual-interface chip, enabling contactless operation via NFC. In practice, however, the NFC interface has been used almost exclusively by proof-of-concept Android applications distributed by CNP-FNMT through Google Play and the Spanish DNIe website, and a LGPLv3-licensed example Android app SDK. Among these, the DNIeRemote application is particularly relevant to OpenSC development, as it allows a computer to access a DNIe 3.0 card through the NFC interface of a smartphone, eliminating the need for a dedicated smart card reader, like the [Remote Smart Card Reader app provided by the vsmartcard project](https://frankmorgner.github.io/vsmartcard/remote-reader/README.html). Currently, both OpenSC and the DGP-distributed PKCS#11 DNIe module only supports DNIe 3.0 cards through the traditional contact interface, and cannot work with vsmartcard's Remote Smart Card Reader due to the different wire protocol of the NFC interface.

In 2021, the DGP began issuing DNIe 4.0 cards. According to [official sources](https://www.dnielectronico.es/PortalDNIe/PRF1_Cons02.action?pag=REF_110), this version is primarily a visual refresh of the DNIe 3.0, introduced to comply with EU Regulation 2019/1157.

Resources:

* The [official home page](http://www.dnielectronico.es) for the Spanish DNIe
* The [official page for the Spanish DNIe 3.0/4.0 electrical specifications](https://www.dnielectronico.es/PortalDNIe/PRF1_Cons02.action?pag=REF_083)
* [A technical specification sheet for the Spanish DNIe 3.0/4.0](https://www.dnielectronico.es/PDFs/CARACTERISTICAS_TECNICAS_DNIe_3.0.pdf) under the official home page domain

## Card capabilities

This is what is present when you dump the pkcs15 structure of a DNIe

```bash
PKCS#15 Card [DNI electrónico]:
	Version        : 0
	Serial number  : 06B62458828132
	Manufacturer ID: DGP-FNMT
	Flags          : Login required, PRN generation

PIN [PIN1]
	Object Flags   : [0x3], private, modifiable
	ID             : 01
	Flags          : [0x211], case-sensitive, initialized, integrity-protected
	Length         : min_len:4, max_len:16, stored_len:8
	Pad char       : 0x00
	Reference      : 1
	Type           : ascii-numeric

Private RSA Key [KprivAutenticacion]
	Object Flags   : [0x3], private, modifiable
	Usage          : [0xC], sign, signRecover
	Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
	ModLength      : 2048
	Key ref        : 1 (0x1)
	Native         : yes
	Path           : 3f003f110101
	Auth ID        : 01
	ID             : 4130364236323435383832383133323230313031313131313634303236
	GUID           : {41303642-3632-3435-3838-323831333232}

Private RSA Key [KprivFirmaDigital]
	Object Flags   : [0x3], private, modifiable
	Usage          : [0x20C], sign, signRecover, nonRepudiation
	Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
	ModLength      : 2048
	Key ref        : 2 (0x2)
	Native         : yes
	Path           : 3f003f110102
	Auth ID        : 01
	ID             : 4630364236323435383832383133323230313031313131313634303236
	GUID           : {46303642-3632-3435-3838-323831333232}

Public RSA Key [KpuAutenticacion]
	Object Flags   : [0x3], private, modifiable
	Usage          : [0xC0], verify, verifyRecover
	Access Flags   : [0x12], extract, local
	ModLength      : 2048
	Key ref        : 1
	Native         : yes
	Path           : 3f003f110101
	Auth ID        : 01
	ID             : 4130364236323435383832383133323230313031313131313634303236

Public RSA Key [KpuFirmaDigital]
	Object Flags   : [0x3], private, modifiable
	Usage          : [0x2C0], verify, verifyRecover, nonRepudiation
	Access Flags   : [0x12], extract, local
	ModLength      : 2048
	Key ref        : 2
	Native         : yes
	Path           : 3f003f110102
	Auth ID        : 01
	ID             : 4630364236323435383832383133323230313031313131313634303236

X.509 Certificate [CertAutenticacion]
	Object Flags   : [0x3], private, modifiable
	Authority      : no
	Path           : 3f0060817004
	ID             : 4130364236323435383832383133323230313031313131313634303236
	GUID           : {41303642-3632-3435-3838-323831333232}

X.509 Certificate [CertFirmaDigital]
	Object Flags   : [0x3], private, modifiable
	Authority      : no
	Path           : 3f0060817005
	ID             : 4630364236323435383832383133323230313031313131313634303236
	GUID           : {46303642-3632-3435-3838-323831333232}

X.509 Certificate [CertCAIntermediaDGP]
	Object Flags   : [0x2], modifiable
	Authority      : no
	Path           : 3f0060617006
	ID             : 5330364236323435383832383133323230313031313131313634303236
	GUID           : {53303642-3632-3435-3838-323831333232}
	Encoded serial : 02 10 642066C9997BAEE14402DA6EA422D649

Reading data object <0>
applicationName: 0000
Label:           ADMIN_DatosFiliacion
applicationOID:  NONE
Path:            3f0060317001
Auth ID:         01
Reading data object <1>
applicationName: 0000
Label:           ADMIN_ImagenFacial
applicationOID:  NONE
Path:            3f0060317002
Auth ID:         01
Reading data object <2>
applicationName: 0000
Label:           ADMIN_ImagenFirma
applicationOID:  NONE
Path:            3f0060317003
Auth ID:         01
```

Additionally there are some other data available:

* Certificates used to stablish Secure Messaging Channel (according CWA14890-1 standard),
  * EF 3F00/6020: ICC intermediate CA certificate in CVC (CardVerifiableCertificate) format,
  * EF 3F00/601F: ICC Certificate (CVC format).

* Extra information about card:
  * EF 3F00/0006: IDESP (Card Serial Number),
  * EF 3F00/2F03: DNIe card software version (not available in every cards),
  * Chip Serial Number can be obtained by mean of APDU 90 B8 00 00 07,
  * There is no EF(ATR) nor ED(DIR) files available,
  * PIN related data are stored at 3F00/0000, but no read/write operations available on this EF.

DataObjects shown in pkcs15 structure are only readable with special unpublished software at Police's office

Public and Private keys are stored together in the same EF (A proprietary file type 15 is returned from FCI) so public keys are not readable, and need to be extracted from certificates

The card stores Three certificates (PIN-CHV1 protected):

* User Certificate (Authentication),
* User Certificate (Signature),
* Intermediate CA Certificate.

These certificates are stored in compressed format.

## ATR Info

From DGP's page:

```text
Tag Value Meaning
TS  0x3B  Direct Convention
T0  0x7F  Y1=0x07=0111; TA1,TB1 y TC1 present.
          K=0x0F=1111; 15 historical bytes
TA1 0x38  FI (Factor de conversión de la tasa de reloj) = 744
          DI (Factor de ajuste de la tasa de bits) = 12
          Máx 8 Mhz.
TB1 0x00  No Vpp (programming voltage) required
TC1 0x00  No additional wait time required.
H1  0x00  Not used
H2  0x6A  Issuer data (10 bytes)
H3  0x44  'D'
H4  0x4E  'N'
H5  0x49  'I'
H6  0x65  'e' ( stands for 'DNIe' keyword )
H7  Incorporated Match-On-Card technology issuer
    0x10  SAGEM
    0x20  SIEMENS
H8  0x02  IC manufacturer: STMicroelectronics.
H9  0x4C
H10 0x34  IC type: 19WL34
H11 0x01  MSB OS version: 1
H12 0x1v  LSB OS version: 1v
H13 Life cycle phase
    0x00  pre-personalization.
    0x01  personalization.
    0x03  user.
    0x0F  final.
H14 0xss
H15 0xss  (2) Status bytes

H13-H15: 0x03 0x90 0x00 user phase
H13-H15: 0x0F 0x65 0x81 final phase: unoperative card
```

The ATR's and masks used in OpenSC are:

* Running (user lifecycle) cards:
  * `3B:7F:00:00:00:00:6A:44:4E:49:65:00:00:00:00:00:00:03:90:00`,
  * `FF:FF:00:FF:FF:FF:FF:FF:FF:FF:FF:00:00:00:00:00:00:FF:FF:FF`.
* Invalidated (final phase) cards
  * `3B:7F:00:00:00:00:6A:44:4E:49:65:00:00:00:00:00:00:0F:65:81`,
  * FF:FF:00:FF:FF:FF:FF:FF:FF:FF:FF:00:00:00:00:00:00:FF:FF:FF.

## Supported algorithms

* DNIe supports PKCS1 padding and SHA1 hashes with 1024/2048bit RSA keys

## Known issues and incompatibilities

* Secure Messaging according CWA14890 standard is used to perform any critical task
* DNIe does not support Logical Channels as described in ISO7816-4. So every communication
with the card require the use of a single SM channel. This leads to many concurrency problems
* DNIe requires PIN (CHV1) to access UserCertificates
* As shown in pkcs15 tree, public and private keys are stored together in the same EF file, this
makes `pkcs15-tool --read-public-keys` to fail
* The chip is prone to self-destroy on voltage level changes. NEVER plug/unplug reader with DNIe inserted
* Some critical task needs to be done at Police's station with special hard/soft: Change/unlock pin, Certificate renewal
* DNIe does not support APDU chaining, instead envelope cmd is issued by mean of a proprietary APDU (CLA 90 + envelope cmd)

## OpenDNIe implementation

Current implementation of DNIe OpenSC driver consists in:

* `card-dnie.c` driver,
* `pkcs15-dnie.c` emulator layer,
* `cwa14890.c` card-independent implementation for CWA14890 standard for Secure Messaging,
* `cwa-dnie.c` DNIe Data provider for cwa14890.c, that provides (local) keys and certificates required to complain with cwa14890 SM protocol,
* a wrap function for sc_transmit_apdu() method, that catches al apdu requests, and translates into SM format when required,
* several glue patches.
