# CardOS

There are two versions of CardOS cards. The first old, version 4 works in OpenSC including card provisioning using `cardos-tool`. The other version 5 works for using in PKCS#11 interface, but the provisioning and management operations are not supported in `cardos-tool`, because of lack of publicly available documentation.

## Siemens CardOS M4

Siemens CardOS M4 smart card should work fine with OpenSC.

Supported smart cards are the Aladdin eToken PRO and the [CryptoIdentityItsec Eutron ITSEC-I](Eutron-CryptoIdentity-ITSEC-I-ITSEC-P) USB tokens.

Siemens CardOS M4 does not allow a key to be used for signing and decryption. OpenSC has a workaround for this restriction, you can generate or store a private key with the `--split-key` flag which will store the key twice, with different usage options, but hide this detail.

Some documentation is available from Aladdin for their eToken PRO, but for an in-depth documentation you need the Siemens card manual, which requires signing an NDA.

The versions of CardOS M4 are: M4.0, M4.01, M4.2, M4.3, M4.3b, M4.2b, M4.2c and M4.4 in this order.

Also note that M4.0 needs special "packages" (i.e. signed firmware addons by siemens) installed to work properly. Best not to use that ancient version if you can.

## CardOS 5

There is no publicly available specification for these cards so they are supported to the best effort, mostly for reading and basic cryptographic RSA operations. The cards are capable of EC operations, but the support for it is not implemented yet (2017).

There are known version 5.0 and 5.3, which differ only slightly.
