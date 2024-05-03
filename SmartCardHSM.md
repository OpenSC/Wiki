# SmartCardHSM

The SmartCard-HSM is a lightweight hardware security module in a smart card form factor. The [Nitrokey HSM](https://www.nitrokey.com/) is a lightweight hardware security module in a USB key form factor containing the SmartCard-HSM. Both are 100% compatible and provide a remote-manageable secure key store for RSA and ECC keys.

The SmartCard-HSM is available as USB key, ID-1 card with contact/contactless interface, as ID-000 plug-in and MicroSD card. It can be purchased at [authorized resellers](http://www.smartcard-hsm.com/buy.html). The Nitrokey HSM can be purchased in [Nitrokey's online shop](https://shop.nitrokey.com/shop).

An alternative and light-weight PKCS#11 module for read-only access is available with the SmartCard-HSM for [Embedded Devices](https://github.com/CardContact/sc-hsm-embedded/wiki/PKCS11) project.

CardContact provides free development samples for open source developers. For commercial use we offer a SmartCard-HSM SDK with certified drivers and extended support for Java.

Unless stated otherwise, a SmartCard-HSM is usually shipped uninitialized, meaning that no SO-PIN is set. You will first need to perform an initialization to set the SO-PIN and initial user PIN. See section [Initialize the Device](#initialize-the-device).

Please note, that the SmartCard-HSM is *not* compatible with the `pkcs15-init` command. In particular it does *not* support `pkcs15-init` to import a key from PKCS#12 files. Doing so will just create certificate objects and the private key metadata, but no key. Please use the [Smart Card Shell](https://www.openscdp.org/scsh3/) to import keys and certificates from PKCS#12 files.

Further test scripts can be found in the [Smart Card Shell Script Collection](https://www.openscdp.org/scripts/sc-hsm/jsdoc/index.html).

## Reader support

For RSA 2048 operations and write support, the SmartCard-HSM requires a card reader supporting extended length APDUs. In our tests, card reader from Identive (formerly SCM) performed best.

Omnikey readers require a registry setting to enable TPDU mode and support for extended length APDU. If you encounter problems with Omnikey readers, try setting the following registry key:

```powershell
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\CardMan]
"TPDU_T1Mode"=dword:00000001
```

or on Linux set in `/etc/omnikey.ini`

```powershell
[Reader]
TPDU_T1Mode=1
```

However, setting T1Mode breaks PIN verification using the PIN pad on the Omnikey 3821 reader.

## Using the PKCS#11 module in Firefox and Thunderbird

After installation of OpenSC you must register the PKCS11 module in Firefox:

1. Open the Firefox preferences dialog. Choose "Advanced" > "Encryption" > "Security Devices"
2. Choose "Load"
3. Enter a name for the security module, such as "OpenSC"
4. Choose "Browse..." to find the location of the PKCS11 module on your local computer (Usually `c:\WINDOWS\System32\opensc-pkcs11.dll` or `/usr/local/lib/opensc-pkcs11.so`)

## Using the SmartCard-HSM with the CSP Minidriver

After installing OpenSC on Windows, you can register the SmartCard-HSM for use with Microsoft applications like Internet Explorer or Outlook.

Save the following to a file sc-hsm.reg and double-click to import it into the registry. Please make sure you replace the quotes by straight quotes.

```powershell
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Cryptography\Calais\SmartCards\SmartCard-HSM]
"ATR"=hex:3b,fe,18,00,00,81,31,fe,45,80,31,81,54,48,53,4d,31,73,80,21,40,81,07,fa
"ATRMask"=hex:ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff
"Crypto Provider"="OpenSC CSP"
"Smart Card Key Storage Provider"="Microsoft Smart Card Key Storage Provider"
"80000001"="C:\Program Files\OpenSC Project\OpenSC\minidriver\opensc-minidriver.dll"

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Cryptography\Calais\SmartCards\SmartCard-HSM-CL]
"ATR"=hex:3B,8E,80,01,80,31,81,54,48,53,4D,31,73,80,21,40,81,07,18
"ATRMask"=hex:ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff,ff
"Crypto Provider"="OpenSC CSP"
"Smart Card Key Storage Provider"="Microsoft Smart Card Key Storage Provider"
"80000001"="C:\Program Files\OpenSC Project\OpenSC\minidriver\opensc-minidriver.dll"
```

If you plan to use 32-bit applications on Windows with 64-bit, then you need to install both versions
of OpenSC, the 64-bit and the 32-bit version. The same registry setting as above must be applied to
`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Wow6432Node\Cryptography\Calais\SmartCards`.

## Using opensc-explorer

The SmartCard-HSM is an applet installed on a JavaCard. With opensc-explorer you will first need to select the applet before you can issue further commands.

```powershell
OpenSC Explorer version 0.13.0-pre1
Using reader with a card: SCM SCR 3310 00 00
OpenSC [3F00]> cd aid:E82B0601040181C31F0201
OpenSC [E82B/0601/0401/81C3/1F02/01]> ls
FileID  Type  Size
 2F02    wEF   436
 C401    wEF    44
 CE01    wEF  1023
 C402    wEF    49
 CE02    wEF   781
 C403    wEF    49
 CE03    wEF   765
 C404    wEF    49
 CE04    wEF   770
 C405    wEF    47
 CE05    wEF   796
 CC00 unable to select file, File not found
 CC01 unable to select file, File not found
 CC02 unable to select file, File not found
 CC03 unable to select file, File not found
 CC04 unable to select file, File not found
 CC05 unable to select file, File not found
OpenSC [E82B/0601/0401/81C3/1F02/01]>
```

Don't be worry about the CCxx files not being found. These file identifiers are reserved as references for key objects and can not be found by the opensc-explorer.

The card PIN is referenced using the PIN id 0x81. In `opensc-explorer` you will need to enter

```sh
OpenSC [E82B/0601/0401/81C3/1F02/01]> verify chv129
Please enter PIN:
Code correct.
OpenSC [E82B/0601/0401/81C3/1F02/01]>
```

For changing the PIN you need to enter the old and new PIN as ASCII string:

```sh
OpenSC [E82B/0601/0401/81C3/1F02/01]> change chv129 "648219" "123456"
PIN changed.
OpenSC [E82B/0601/0401/81C3/1F02/01]> 
```

For changing the SO-PIN you need to enter the old and new PIN as ASCII string:

```sh
OpenSC [E82B/0601/0401/81C3/1F02/01]> change chv136 "3537363231383830" "3537363231383830"
PIN changed.
OpenSC [E82B/0601/0401/81C3/1F02/01]> 
```

## Using pkcs15-tool

A SmartCard-HSM equipped with test certificates from the [support package](https://www.openscdp.org/scripts/sc-hsm/jsdoc/index.html) contains one RSA and four ECC keys:

```sh
$ pkcs15-tool -D
Using reader with a card: SCM SCR 3310 [CCID Interface] (21120843305113) 00 00
PKCS#15 Card [SmartCard-HSM]:
        Version        : 0
        Serial number  : UTCC0200062
        Manufacturer ID: www.CardContact.de
        Flags          : 

PIN [UserPIN]
        Object Flags   : [0x3], private, modifiable
        ID             : 01
        Flags          : [0x81A], local, unblock-disabled, initialized, exchangeRefData
        Length         : min_len:4, max_len:16, stored_len:0
        Pad char       : 0x00
        Reference      : 129 (0x81)
        Type           : ascii-numeric
        Tries left     : 3

PIN [SOPIN]
        Object Flags   : [0x1], private
        ID             : 02
        Flags          : [0x9E], local, change-disabled, unblock-disabled, initialized, soPin
        Length         : min_len:16, max_len:16, stored_len:0
        Pad char       : 0x00
        Reference      : 136 (0x88)
        Type           : bcd
        Tries left     : 3

Private RSA Key [Joe Doe (RSA2048)]
        Object Flags   : [0x1], private
        Usage          : [0x2E], decrypt, sign, signRecover, unwrap
        Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
        ModLength      : 2048
        Key ref        : 1 (0x1)
        Native         : yes
        Path           : e82b0601040181c31f0201::
        Auth ID        : 01
        ID             : 01
        GUID           : {770ef182-4ff5-c00f-0a60-8a9b261ae0a8}

Private EC Key [Joe Doe (ECC-SECP256)]
        Object Flags   : [0x1], private
        Usage          : [0x104], sign, derive
        Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
        FieldLength    : 256
        Key ref        : 2 (0x2)
        Native         : yes
        Path           : e82b0601040181c31f0201::
        Auth ID        : 01
        ID             : 02
        GUID           : {d6fec6cb-2c43-1ab0-8742-a8bdb4fd4eab}

Private EC Key [Joe Doe (ECC-SECP192)]
        Object Flags   : [0x1], private
        Usage          : [0x104], sign, derive
        Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
        FieldLength    : 192
        Key ref        : 3 (0x3)
        Native         : yes
        Path           : e82b0601040181c31f0201::
        Auth ID        : 01
        ID             : 03
        GUID           : {2db6a307-0241-cf86-5b48-33c5f9636804}

Private EC Key [Joe Doe (ECC-BP224)]
        Object Flags   : [0x1], private
        Usage          : [0x104], sign, derive
        Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
        FieldLength    : 224
        Key ref        : 4 (0x4)
        Native         : yes
        Path           : e82b0601040181c31f0201::
        Auth ID        : 01
        ID             : 04
        GUID           : {d8a5d028-f382-7643-82b0-438766926582}

Private EC Key [Joe Doe (ECC-BP320)]
        Object Flags   : [0x1], private
        Usage          : [0x104], sign, derive
        Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
        FieldLength    : 320
        Key ref        : 5 (0x5)
        Native         : yes
        Path           : e82b0601040181c31f0201::
        Auth ID        : 01
        ID             : 05
        GUID           : {64bb4c16-63c2-3216-f4e5-e10ff1d47506}

Private RSA Key [Joe Doe (RSA1536)]
        Object Flags   : [0x1], private
        Usage          : [0x2E], decrypt, sign, signRecover, unwrap
        Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
        ModLength      : 1536
        Key ref        : 6 (0x6)
        Native         : yes
        Path           : e82b0601040181c31f0201::
        Auth ID        : 01
        ID             : 06
        GUID           : {14027c5b-0971-ed85-9716-1bb8083a1496}

Private RSA Key [Joe Doe (RSA1024)]
        Object Flags   : [0x1], private
        Usage          : [0x2E], decrypt, sign, signRecover, unwrap
        Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
        ModLength      : 1024
        Key ref        : 7 (0x7)
        Native         : yes
        Path           : e82b0601040181c31f0201::
        Auth ID        : 01
        ID             : 07
        GUID           : {f40c87ed-3026-a3a3-2f16-4e339fce6ff9}

X.509 Certificate [Joe Doe (RSA2048)]
        Object Flags   : [0x0]
        Authority      : no
        Path           : ce01
        ID             : 01
        GUID           : {770ef182-4ff5-c00f-0a60-8a9b261ae0a8}
        Encoded serial : 02 08 02B00F6CEC251CE5
X.509 Certificate [Joe Doe (ECC-SECP256)]
        Object Flags   : [0x0]
        Authority      : no
        Path           : ce02
        ID             : 02
        GUID           : {d6fec6cb-2c43-1ab0-8742-a8bdb4fd4eab}
        Encoded serial : 02 08 02F9D76BFF7291B1
X.509 Certificate [Joe Doe (ECC-SECP192)]
        Object Flags   : [0x0]
        Authority      : no
        Path           : ce03
        ID             : 03
        GUID           : {2db6a307-0241-cf86-5b48-33c5f9636804}
        Encoded serial : 02 08 02E83DD1B2C2C857
X.509 Certificate [Joe Doe (ECC-BP224)]
        Object Flags   : [0x0]
        Authority      : no
        Path           : ce04
        ID             : 04
        GUID           : {d8a5d028-f382-7643-82b0-438766926582}
        Encoded serial : 02 08 02E3A790DD0D1761
X.509 Certificate [Joe Doe (ECC-BP320)]
        Object Flags   : [0x0]
        Authority      : no
        Path           : ce05
        ID             : 05
        GUID           : {64bb4c16-63c2-3216-f4e5-e10ff1d47506}
        Encoded serial : 02 08 026B07E37DBFA205
X.509 Certificate [Joe Doe (RSA1536)]
        Object Flags   : [0x0]
        Authority      : no
        Path           : ce06
        ID             : 06
        GUID           : {14027c5b-0971-ed85-9716-1bb8083a1496}
        Encoded serial : 02 08 02A7EEFA99415EBF
X.509 Certificate [Joe Doe (RSA1024)]
        Object Flags   : [0x0]
        Authority      : no
        Path           : ce07
        ID             : 07
        GUID           : {f40c87ed-3026-a3a3-2f16-4e339fce6ff9}
        Encoded serial : 02 08 02C05C90657208BC
```

## Using `pkcs11-tool`

### Initialize the device

Initializing - and thereby erasing all keys, certificates and data elements - requires the following command

```sh
$ sc-hsm-tool --initialize --so-pin 3537363231383830 --pin 648219
```

or

```sh
$ pkcs11-tool --module /usr/local/lib/opensc-pkcs11.so --init-token --init-pin --so-pin=3537363231383830 --new-pin=648219 --label="test" --pin=648219
```

If the SmartCard-HSM was never initialized before, the presented SO-PIN is stored as future SO-PIN for this device. Any later initialization requires the presentation of the same SO-PIN. Please make sure to select your own values for SO-PIN and user PIN if used in a production environment.

The SO-PIN must be composed of 16 hexadecimal characters. The value is internally converted into an 8 byte key value. The SO-PIN has a retry counter of 15 and can not be unblocked. Blocking the SO-PIN will prevent any further token initialization or PIN unblock.

Please note that while changing the PIN later is possible, changing its *size* (length) is not. Current card version 1.2 does not allow this without reinitializing the card.

The parameter `--label` is required by PKCS#11, but has not effect on version 0.13. OpenSC versions from the current master support storing the label.

For SmartCard-HSMs in a version before 1.0, the `--init-pin` option can only be used immediately after the `--init-token` command. To reset a lost PIN you will need to reinitialize the device. For SmartCard-HSMs with version 1.0 or above the `--init-pin` command can be used at any time, allowing to unblock and reset the PIN using the SO-PIN.

To change the SO-PIN (Version 1.0 or higher) you can use

```sh
$ pkcs11-tool --module /usr/local/lib/opensc-pkcs11.so --login --login-type so --so-pin 3537363231383830 --change-pin --new-pin 0123456789012345
```

Security Considerations: The SO-PIN allows to reset the retry counter for the PIN and also allows to set the PIN to a new value. Please make sure, that the SO-PIN is kept secure at any time. If you don't ever want to reset the card, we suggest to set the SO-PIN to an 8 byte random value.

To change the PIN you can use

```sh
$ pkcs11-tool --login --pin 648219 --change-pin --new-pin 123456
```

To unblock and change the PIN using the SO-PIN you can use

```sh
$ pkcs11-tool --login --login-type so --so-pin=3537363231383830 --init-pin --new-pin=648219
```

### Generate key pair

Use the following command to generate a key pair:

```sh
$ pkcs11-tool --module /usr/local/lib/opensc-pkcs11.so -l --pin 648219 --keypairgen --key-type rsa:1024 --id 10
Using slot 1 with a present token (0x1)
Key pair generated:
Private Key Object; RSA
  label:      Private Key
  ID:         10
  Usage:      decrypt, sign, unwrap
Public Key Object; RSA 1024 bits
  label:      Private Key
  ID:         10
  Usage:      encrypt, verify, wrap
```

or for ECC keys:

```sh
$ pkcs11-tool --module /usr/local/lib/opensc-pkcs11.so --login --pin 648219 --keypairgen --key-type EC:prime256v1 --label mykey
```

The SmartCard-HSM driver extracts required PKCS#11 public key object from certificates stored on the device. For newly generated key pairs without a certificate the certificate signing request is stored instead.

To save the generated public key in Subject Public Key Information format as per RFC3280 use the following command

```sh
$ pkcs11-tool --module /usr/local/lib/opensc-pkcs11.so -l --pin 648219 --id 10 --read-object --type pubkey --output-file pubkey.spki
```

### Store certificates and data

Use the following commands to store certificates and data:

```sh
$ pkcs11-tool --module /usr/local/lib/opensc-pkcs11.so -l --pin 648219 --write-object testcert.der --type cert --id 10
```

and

```sh
$ pkcs11-tool --module /usr/local/lib/opensc-pkcs11.so -l --pin 648219 --write-object testcert.der --type data --label testdata
```

### Delete objects

For deleting objects use one of the following commands

```sh
$ pkcs11-tool --module /usr/local/lib/opensc-pkcs11.so -l --pin 648219 --delete-object --type cert --id 10
$ pkcs11-tool --module /usr/local/lib/opensc-pkcs11.so -l --pin 648219 --delete-object --type privkey --id 10
$ pkcs11-tool --module /usr/local/lib/opensc-pkcs11.so -l --pin 648219 --delete-object --type data --label testdata
```

## Using OpenSSL with `engine-pkcs11`

> Usage of engines is deprecated from OpenSSL 3.0

### Generate a key pair and a self-signed certificate

Use the following command to generate a key pair:

```sh
$ pkcs11-tool --module /usr/local/lib/opensc-pkcs11.so -l --pin 648219 --keypairgen --key-type rsa:2048 --id 10
```

Start openssl to get the openssl prompt and enter

```sh
OpenSSL> engine -t dynamic -pre SO_PATH:/usr/local/lib/engines/engine_pkcs11.so -pre ID:pkcs11 -pre LIST_ADD:1 -pre LOAD -pre MODULE_PATH:/usr/local/lib/pkcs11/opensc-pkcs11.so
OpenSSL> req -engine pkcs11 -new -key 0:10 -keyform engine -out cert.pem -text -x509 -days 3640
```

This results in a file `cert.pem` that contains the self-signed certificate.
If your SmartCard-HSM is not inserted into the first card reader, then please change `0:10` to `slotid:keyid` accordingly.
Use the following line to convert to DER format and load the certificate into the SmartCard-HSM:

```sh
$ openssl x509 -in cert.pem -out cert.der -outform der
$ pkcs11-tool --module /usr/local/lib/opensc-pkcs11.so -l --pin 648219 --write-object cert.der --type cert --id 10
```

## Using key backup and restore

The SmartCard-HSM provides for a secure key backup and restore functionality. This mechanism allows to encrypt and export a key generated on a SmartCard-HSM and to later import that key into the same or a different SmartCard-HSM. The scheme can be used to

* Backup a key for disaster recovery (e.g. in a Root-CA)
* Extend the number of keys by offloading unused keys to an external database
* Operate multiple SmartCard-HSMs in a cluster to improve performance and allow fail-over
* Implement a key escrow scheme

In order to export or import keys, the SmartCard-HSM must be initialized with a Device Key Encryption Key (DKEK). The DKEK is a 256-Bit AES key.

The DKEK must be set during initialization and before any other keys are generated. For a device initialized without a DKEK, keys can never be exported.

A DKEK is imported into a SmartCard-HSM using a preselected number of key shares. Each key share is given to a key custodian and only all key shares together assemble the DKEK. Key shares are individually imported and are assembled within the SmartCard-HSM. Key shares can be imported independently of time and location, allowing to pass a half-initialized device between key custodians until all shares have been imported.

The key management procedure starts with the creation of a key share. This can be achieved using the `sc-hsm-tool`:

```sh
asc@calzone:~/tmp$ sc-hsm-tool --create-dkek-share dkek-share-1.pbe
Using reader with a card: SCM SCR 355 [CCID Interface] 00 00

The DKEK share will be enciphered using a key derived from a user supplied password.
The security of the DKEK share relies on a well chosen and sufficiently long password.
The recommended length is more than 10 characters, which are mixed letters, numbers and
symbols.

Please keep the generated DKEK share file in a safe location. We also recommend to keep a
paper printout, in case the electronic version becomes unavailable. A printable version
of the file can be generated using "openssl base64 -in <filename>".
Enter password to encrypt DKEK share : 

Please retype password to confirm : 

Enciphering DKEK share, please wait...
DKEK share created and saved to dkek-share-1.pbe
```

The DKEK will be generated using the random number generator in the SmartCard-HSM and is then encrypted using Password-based-Encryption with the password supplied by the key custodian. The procedure is repeated for each key custodian. The resulting key share file and the password must be kept secure by the respective key custodian.

When all key custodians have generated their key share, a SmartCard-HSM can be initialized and the DKEK can be created from these key shares (two in this example):

```sh
sc@calzone:~/tmp$ sc-hsm-tool --initialize --so-pin 3537363231383830 --pin 648219 --dkek-shares 2
Using reader with a card: SCM SCR 355 [CCID Interface] 00 00
asc@calzone:~/tmp$ sc-hsm-tool
Using reader with a card: SCM SCR 355 [CCID Interface] 00 00
Version              : 1.2
User PIN tries left  : 3
DKEK shares          : 2
DKEK import pending, 2 share(s) still missing
```

The `sc-hsm-tool` called without arguments reports that the import of DKEK shares is not yet completed as 2 shares are still missing.

The first key custodian imports his key share using:

```sh
asc@calzone:~/tmp$ sc-hsm-tool --import-dkek-share dkek-share-1.pbe
Using reader with a card: SCM SCR 355 [CCID Interface] 00 00
Enter password to decrypt DKEK share : 

Deciphering DKEK share, please wait...
DKEK share imported
DKEK shares          : 2
DKEK import pending, 1 share(s) still missing
```

The second key custodian imports his key share the same way:

```sh
asc@calzone:~/tmp$ sc-hsm-tool --import-dkek-share dkek-share-2.pbe
Using reader with a card: SCM SCR 355 [CCID Interface] 00 00
Enter password to decrypt DKEK share : 

Deciphering DKEK share, please wait...
DKEK share imported
DKEK shares          : 2
DKEK key check value : 7A5D817FE55E8F74
```

As all key shares have been imported, the resulting DKEK is assembled internally and a key check value is calculated. The key check value represents the DKEK without disclosing it's value. Two SmartCard-HSMs with the same DKEK key check value contain the same DKEK. The DKEK key check value can be obtained at any time using the `sc-hsm-tool` command without arguments.

With the initialized SmartCard-HSM new keys can be generated using the commands shown above.

```sh
asc@calzone:~/tmp$ pkcs11-tool --module /usr/local/lib/opensc-pkcs11.so -l --pin 648219 --keypairgen --key-type rsa:2048 --id 10
Using slot 0 with a present token (0x0)
Key pair generated:
Private Key Object; RSA 
  label:      Private Key
  ID:         10
  Usage:      decrypt, sign, unwrap
Public Key Object; RSA 2048 bits
  label:      Private Key
  ID:         10
  Usage:      encrypt, verify, wrap
```

The resulting key can now be exported from the SmartCard-HSM. In order to do so, you will need the internal key id of the key, which can be obtained using `pkcs15-tool`:

```sh
asc@calzone:~/tmp$ pkcs15-tool -D
Using reader with a card: SCM SCR 355 [CCID Interface] 00 00
PKCS#15 Card [SmartCard-HSM]:
        Version        : 0
        Serial number  : UTTMRN08223
        Manufacturer ID: www.CardContact.de
        Flags          : 

PIN [UserPIN]
---8<------8<------8<------8<------8<---
Private RSA Key [Private Key]
        Object Flags   : [0x3], private, modifiable
        Usage          : [0x2E], decrypt, sign, signRecover, unwrap
        Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
        ModLength      : 2048
        Key ref        : 1 (0x1)
        Native         : yes
        Path           : e82b0601040181c31f0201::
        Auth ID        : 01
        ID             : 10
        GUID           : {ed416326-6607-d52c-d194-72f7f51915a9}
---8<------8<------8<------8<------8<---
```

The important information is in the "Key ref" field. This identifier must be provided in the `--key-reference` argument of the `--wrap-key` command:

```sh
asc@calzone:~/tmp$ sc-hsm-tool --wrap-key wrap-key.bin --key-reference 1 --pin 648219
Using reader with a card: SCM SCR 355 [CCID Interface] 00 00
asc@calzone:~/tmp$ ls -la wrap-key.bin 
-rw-rw-r-- 1 asc asc 1696 Jul 17 19:15 wrap-key.bin
</code></pre>

The resulting file contains a key description, the optional certificate and the key value encrypted under the DKEK. The key value and it's meta data is protected by a cryptographic checksum against modifications.

Importing the key into the same or a different SmartCard-HSM with the same DKEK can be done using:

<pre><code>
asc@calzone:~/tmp$ sc-hsm-tool --unwrap-key wrap-key.bin --key-reference 10 --pin 648219
Using reader with a card: SCM SCR 355 [CCID Interface] 00 00
Wrapped key contains:
  Key blob
  Private Key Description (PRKD)
  Certificate
Key successfully imported
asc@calzone:~/tmp$ pkcs15-tool -D
Using reader with a card: SCM SCR 355 [CCID Interface] 00 00
PKCS#15 Card [SmartCard-HSM]:
        Version        : 0
        Serial number  : UTTMRN08223
        Manufacturer ID: www.CardContact.de
        Flags          : 
---8<------8<------8<------8<------8<---
Private RSA Key [Private Key]
        Object Flags   : [0x3], private, modifiable
        Usage          : [0x2E], decrypt, sign, signRecover, unwrap
        Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
        ModLength      : 2048
        Key ref        : 1 (0x1)
        Native         : yes
        Path           : e82b0601040181c31f0201::
        Auth ID        : 01
        ID             : 10
        GUID           : {ed416326-6607-d52c-d194-72f7f51915a9}

Private RSA Key [Private Key]
        Object Flags   : [0x3], private, modifiable
        Usage          : [0x2E], decrypt, sign, signRecover, unwrap
        Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
        ModLength      : 2048
        Key ref        : 10 (0xA)
        Native         : yes
        Path           : e82b0601040181c31f0201::
        Auth ID        : 01
        ID             : 10
        GUID           : {ed416326-6607-d52c-d194-72f7f51915a9}
---8<------8<------8<------8<------8<---
```

The SmartCard-HSM now contains two identical keys, one with key ref 1 (the original key) and one with key ref 10 (the copy created during import).

The DKEK (and all stored keys) are erased in a subsequent initialization.

### Using a n-of-m threshold scheme

Control over a DKEK share can be split even further using a n-of-m threshold scheme for the DKEK share's password. In such a scheme m key custodians are given a share of the password and n key custodians must come together to decrypt the DKEK share for import.

The threshold scheme can be used to prevent a single point of failure. A lost DKEK share password will render key backups useless as the DKEK can't be re-established. If the DKEK share password is split amongst more key custodians than are necessary to reconstruct the share, then some parts may be lost or unavailable without consequences.

Generating a DKEK share using the threshold scheme requires the arguments `--pwd-shares-threshold` and `--pwd-shares-total`. The later defines the number of password shares while the former defines the number of shares required to calculate the password.

```sh
asc@calzone:~/tmp$ sc-hsm-tool --create-dkek-share dkek-share-1.pbe --pwd-shares-threshold 3 --pwd-shares-total 5
Using reader with a card: SCM SCR 355 [CCID Interface] 00 00

The DKEK will be enciphered using a randomly generated 64 bit password.
This password is split using a (3-of-5) threshold scheme.

Please keep the generated and encrypted DKEK file in a safe location. We also recommend 
to keep a paper printout, in case the electronic version becomes unavailable. A printable version
of the file can be generated using "openssl base64 -in <filename>".


Press <enter> to continue
```

At this stage, the first key custodian is requested to note the password share:

```sh
Share 1 of 5


Prime       : f3:4a:18:9d:e5:19:7c:fb
Share ID    : 1
Share value : 8e:67:74:ba:38:05:d3:bc


Please note ALL values above and press <enter> when finished
```

The code should be written onto a prepared form which the key custodian keeps securely in his custody.

After all key custodians received their share (5 in this case), the encrypted DKEK share can be kept as backup.

When importing a DKEK share that has a threshold password, the following command must be used:

```sh
asc@calzone:~/tmp$ sc-hsm-tool --import-dkek-share dkek-share-1.pbe --pwd-shares-total 3
Using reader with a card: SCM SCR 355 [CCID Interface] 00 00

Deciphering the DKEK for import into the SmartCard-HSM requires 3 key custodians
to present their share. Only the first key custodian needs to enter the public prime.
Please remember to present the share id as well as the share value.

Please enter prime: f3:4a:18:9d:e5:19:7c:fb
```

Only the first key custodian must enter the prime number and then the number of key custodians defined by `--pwd-shares-threshold` are requested to enter their share value:

```sh
Share 1 of 3

Please enter share ID: 1
Please enter share value: 8e:67:74:ba:38:05:d3:bc
```

This is repeated until all key custodians entered their password share. The DKEK share password is then calculated from the provided shares, the DKEK share is decrypted and imported into the SmartCard-HSM.

In the scheme you can mix DKEK shares encrypted with a plain password and DKEK shares whose password is split with the threshold mechanism.
