# US PIV

The [National Institute of Standards and Technology](http://www.nist.gov/), U.S. Department of Commerce has defined a  smart card application. Although not a "national ID card", it is expected to be used widely in the U.S.federal government and its contractors. Cards with this application are commonly referred to as PIV cards.

[NIST Special Publication 800-73-4](https://csrc.nist.gov/pubs/sp/800/73/4/upd1/final) and [related documents](https://csrc.nist.gov/projects/piv/piv-standards-and-supporting-documentation) define PIV. Part 2 of [NIST SP 800-73-4](https://csrc.nist.gov/pubs/sp/800/73/4/upd1/final) defines the ADPU commands accepted by the PIV application on the card. The standard does not define all the commands needed to administer a card, leaving this up to the card vendors and card administration software vendors.

The non-administrative commands are standardized, and so any vendor's card with the PIV application
should inter operate with any vendor's client software. The [pkcs11-tool](https://htmlpreview.github.io/?https://github.com/OpenSC/OpenSC/blob/master/doc/tools/tools.html#pkcs11-tool) can be used to read the objects on the card and to change the user PIN.

The [piv-tool](https://htmlpreview.github.io/?https://github.com/OpenSC/OpenSC/blob/master/doc/tools/tools.html#piv-tool) is provided to allow for some card administration in testing, such as generating a key pair, and loading a certificate or other object on the card. You may need more information from your card vendor.

The PIV is not a PKCS#15 type card, but rather an object based application. OpenSC provides a PKCS#15 emulator to access the certificates and keys, along with the data objects. Thus for example the "X.509 Certificate for PIV Authentication" can be used with PKCS#11 for login or web access.

The YubiKey supports the PIV card interface, details can be found in the [YubiKey PIV introduction](https://developers.yubico.com/yubico-piv-tool/YubiKey_PIV_introduction.html).
There is also [arekinath/PivApplet](https://github.com/arekinath/PivApplet) - PIV (NIST SP 800-73-4) compatible open source JavaCard applet.

## Support overview

* OpenSC 0.11.1 did not search arbitrary cards for the PIV application, and set the max_send_size and max_recv_size to low for PIV cards. With 0.11.1 you needed to add the ATR of specific vendor's cards to the `opensc.conf`. The ATR of your card can be read using the [opensc-tool](https://htmlpreview.github.io/?https://github.com/OpenSC/OpenSC/blob/master/doc/tools/tools.html#opensc-tool).
* OpenSC 0.11.2 added support for certificates that are gzip'ed. But only 1024 bit RSA keys are supported.
* OpenSC 0.11.3 added support for 2048 and 3072 bit RSA keys.
* OpenSC 0.11.4 added support to read all the objects on the card via PKCS#11, pkcs11-tool and pkcs15-tool.
* OpenSC 0.11.5 added support for [NIST SP 800-73-2](https://csrc.nist.gov/pubs/sp/800/73/2/final).
* OpenSC 0.11.9 fixed bug: highly compressed certificates were only being partially read. If any problems are found in previous versions, please update to at least this version.
* OpenSC 0.11.10 fixed bug when using piv-tool to authenticate to card using 3DES key.  
* OpenSC 0.12.0 Supported added for [NIST SP 800-73-3](https://csrc.nist.gov/pubs/sp/800/73/3/final) features:
  * Discovery Object - If the Global PIN is to be used, the prompt will be "Global pin" rather then "PIV Card holder pin".
  * Key History Object - If the offCardKeyHistoryFile is prefetched to `~/.eid/<ASCII-HEX encoded SHA-256 hash>` OpenSC will use the certificates found in the file.
  * EC keys for ECDSA signatures are supported. (ECDH is not yet supported.)
  * The card serial number is derived from the CHUID using the FASC-N. If the Agency Code = 9999, and a GUID is present, it is used as the serial number.
  * Piv-tool can now write any object to the card. (Piv-tool continues to be for creating test cards only.)
* OpenSC 0.12.1 bug fixes:
  * Fixed: Support to request the PIN before each Digital Signature Key operation.
  * Fixed: Key usage when using ECDSA with Thunderbird.
  * (Although not PIV specific) a bug was introduced during the release cycle for 0.12.1 where the `pam_krb5` login or Kerberos `kinit` may fail. The circumvention is to set in the `opensc.conf` file `plug_and_play = false;` `C_GetSlotList` with `tokenpresent=1` would return the hotplug slot even if empty as the first slot.
* OpenSC 0.13.0:
  * ECDH with key derivation is now supported via PKCS#11 `C_Derive` using `CKM_ECDH1_COFACTOR_DERIVE` or `CKM_ECDH1_DERIVE`. The KDF must be `CKD_NULL`. See the `pkcs11-tool.c` for an example.
  * `CK_ALWAYS_AUTHENTICATE` is supported for the signing key. This requires the PIN to be entered before cypto opertation when using the signing certificate.
  * Some applications may not yet support the use of CKA_ALWAYS_AUTHENTICATE also know as user_consent in PKCS#15. A new parameter "pin_cache_ignore_user_consent = true;" has been added to the opensc.conf. Uncomment this line to use with these older applications, for example as of 10/2012, versions of Firefox and Thunderbird.
* OpenSC 0.14.0:
  * Added extraction of public key from cert if no object on card
  * Used SPKI encoding for public key data
* OpenSC 0.15.0 added AES support for PIV General Authenticate
* OpenSC 0.18.0 added implementation of keep alive command
* OpenSC 0.24.0 added implementation of PIV secure messaging

For full log of PIV related changes and updates refer to [NEWS file](https://github.com/OpenSC/OpenSC/blob/master/NEWS).

## Links

* [FIPS 201 Approved Product List](https://www.idmanagement.gov/fips201/)
* [NIST SP 800-73-4 Interfaces for Personal Identity Verification](https://csrc.nist.gov/pubs/sp/800/73/4/upd1/final)
* [NIST SP 800-78-4 Cryptographic Algorithms and Key Sizes for Personal Identity Verification](https://csrc.nist.gov/pubs/sp/800/78/4/final)
