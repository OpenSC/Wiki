# Portuguese eID

Official website: <http://www.cartaodocidadao.pt>.

## Card structure (`pkcs15-tool --dump --short`)

Serial number replaced with '*'.

```bash
PKCS#15 Card [CARTAO DE CIDADAO]:
	Version        : 1
	Serial number  : 00000***********
	Manufacturer ID: GEMALTO
	Flags          : Read-only, PRN generation, EID compliant
Card has 3 Authentication object(s).
	PIN  ID:01  Ref:0x81  Tries:3  Auth PIN
	PIN  ID:02  Ref:0x82  Tries:3  Sign PIN
	PIN  ID:03  Ref:0x83  Tries:3  Address PIN
Card has 2 Private key(s).
	RSA[1024]  ID:45  Ref:0x02  AuthID:01
	     CITIZEN AUTHENTI [0x4, sign]
	RSA[1024]  ID:46  Ref:0x01  AuthID:02
	     CITIZEN SIGNATUR [0x200, nonRepudiation]
Card has 0 Public key(s).
Card has 5 Certificate(s).
	Path:3f005f00ef09  ID:45
	Path:3f005f00ef08  ID:46
	Path:3f005f00ef0f  ID:51
	Path:3f005f00ef10  ID:52
	Path:3f005f00ef11  ID:50
Card has 5 Data object(s).
	Path:3f000003      Size:    6  Trace
	Path:3f005f00ef02  Size:15500  Citizen Data        
	Path:3f005f00ef05  AuthID:03   Citizen Address Data
	Path:3f005f00ef06  Size: 4000  SOd               
	Path:3f005f00ef07  Size: 1000  Citizen Notepad
```

## Card History

* 2007-02-14 First card version with a IAS 0.7 V1, EMV-CAP and Precise Bio MOC applets and 1024b keys.
* 2015-06-05 Owner keys size increased to 2048b.
* 2016-01-16 EMV-CAP applet is no longer present.
* 2019-01-05 Owner keys size increased to 3072b.

### Applets used

* 2007-02-14 Gemalto GemXpresso R4 E36/E72 PK IAS 0.7 V1
* 2008-08    Gemalto ICitizen Open v2 IAS 1.01 V2
* 2010-06-15 Gemalto MultiApp ID Citizen 72K CC IAS Classic V3 (IAS 0.7 )
* 2014-04-07 Gemalto MultiApp ID 2.1 MPH117 IAS Classic V3 (IAS 0.7)

#### Document version no longer in circulation

* 3B 65 00 00 D0 00 54 01 31
* 3B 65 00 00 D0 00 54 01 32
* 3B 95 95 40 FF D0 00 54 01 31
* 3B 95 95 40 FF D0 00 54 01 32

#### Document version confirmed

* 3B 7D 95 00 00 80 31 80 65 B0 83 11 00 C8 83 00 90 00 (Version 004.004.21)
* 3B FF 96 00 00 81 31 FE 43 80 31 80 65 B0 85 04 01 20 12 0F FF 82 90 00 D0 (Version 006.008.24)

#### Document version unconfirmed

* 3B 6B 00 00 80 65 B0 83 01 01 74 83 00 90 00
* 3B 6B 00 00 80 65 B0 83 01 03 74 83 00 90 00
* 3B 7A 94 00 00 80 65 A2 01 01 01 3D 72 D6 43
* 3B 7B 94 00 00 80 65 B0 83 01 01 74 83 00 90 00
* 3B 7D 94 00 00 80 31 80 65 B0 83 01 01 90 83 00 90 00
* 3B 7D 95 00 00 80 31 80 65 B0 83 11 00 C8 83 00
* 3B 7D 95 00 00 80 31 80 65 B0 83 11 C0 A9 83 00
* 3B 7D 95 00 00 80 31 80 65 B0 83 11 C0 A9 83 00 90 00
* 3B FF 96 00 00 81 31 80 43 80 31 80 65 B0 85 03 00 EF 12 0F FF 82 90 00 67

## Biometrics

All cards include a Precise Biometric Match-On-Card applet and the correspondent owner fingerprint template.

Documentation or sample code contribution is appreciated.

## Official Middleware

* Official releases are available at: A[utenticação.gov.pt](https://www.autenticacao.gov.pt/cc-software)

## OpenSC support

Starting with version 0.12.0, OpenSC supports the Portuguese eID card - both Authentication and Digital Signature keys.

Full Mac OS X support is available through OpenSCToken.
