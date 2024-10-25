# Finnish FINeID

The FINeID cards are available to private citizens and organizations. All the new personal identity cards are eID cards and they are applied from the police.

The FINeID certificates are issued by the Digital and Population Data Services Agency (DVV). Naturally, one cannot alter the FINeID certificates and keys on the cards.

There are [several generations](https://digisaatio.fi/wiki/Henkil%C3%B6kortti#Versiot) of FINeID cards. OpenSC should work fine with the eID application following the version 1.x specification that is using a PKCS#15 file structure. The version 2 of the FIN specification address the ISO/IEC 7816-15 file structure and somewhat different command parameters to the version 1 specification. The current version of the cards is not supported by OpenSC.

The support for the FINeID [legacy](https://dvv.fi/documents/16079645/17324992/Technology+note+-+ATR+bytes.pdf) cards is implemented in [`card-setcos.c`](https://github.com/OpenSC/OpenSC/blob/master/src/libopensc/card-setcos.c) driver. Refer there for the ATRs of supported cards. Newer FINeID cards are currently unsupported by OpenSC.

The eID card has [two PIN codes](https://dvv.fi/en/citizen-certificate-managing-pin-codes), the basic PIN (PIN1) and the signature PIN (PIN2).

The FINeID cards allow storing extra data (say, home-made PKI keypairs).

## References

* [About cards and certificates at DVV](https://dvv.fi/en/about-certificates) (in English)
* [FINEID specifications at DVV](https://dvv.fi/en/fineid-specifications) (in English)
* [HST at linux.fi wiki](http://linux.fi/wiki/HST) (in Finnish)
* [Henkilökortti at digisaatio.fi wiki](https://digisaatio.fi/wiki/Henkil%C3%B6kortti) (in Finnish)
