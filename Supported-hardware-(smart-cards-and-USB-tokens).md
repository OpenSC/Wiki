# Supported hardware (smart cards and USB tokens)

NB! Unless noted otherwise, OpenSC works only with **contact interface**!

* OpenSC targets only smart cards, so to know if your reader device is support, check the list of [CardReaders](Smart-card-readers-(Linux-and-Mac-OS-X)).
* Proprietary USB tokens will require a (possibly proprietary) USB level driver: PC/SC (preferred) or OpenCT (deprecated)

## National ID Cards

These are usually pre-initialized read-only cards.
Supported eID cards:

* [IAS-ECC](IAS-ECC)
* [UnitedStatesPIV](US-PIV)
* [GermanEid](German-ID-Cards)
* [ItalianCNS](Italian-CNS-and-CIE)
* [ItalianEid](Italian-Infocamere)
* [EstonianEid](Estonian-eID-(EstEID))
* [PortugueseEid](Portuguese-eID)
* [US CAC](US-CAC)
* [UruguayanEid](Uruguayan-eID)

## Generic smart cards

Each entry on this list possibly represents a whole family of cards. See each page to find out which models are supported.
Personalizable cards:

* [OpenPGP Card](OpenPGP-card)
* [MyEID](Aventra-MyEID-PKI-card)
* [WestCOS](WestCOS)
* [SetCOS](Setcos-driver)
* [Oberthur](Oberthur-AuthentIC-applet-v2.2)
* [Cyberflex](Schlumberger-Axalto-Cyberflex)
* [CardOS](Siemens-CardOS-M4)
* [STARCOS](STARCOS-cards)
* [ASEPCOS](Athena-ASEPCOS-ASEKey)
* [SmartCardHsm](SmartCardHSM)
* [Cryptoflex](Schlumberger-Axalto-Cryptoflex)
* [FTCOSPK01C](Feitian-PKI-card)

Read-only cards:

* [AKIS](AKiS-cards)
* [TCOS](TCOS-based-preformatted-cards)
* [MTCOS](MaskTech-cards)

JavaCard applets:

* [MyEID](Aventra-MyEID-PKI-card)
* [Oberthur](Oberthur-AuthentIC-applet-v2.2)
* [MuscleApplet](Muscle-applet)
* [SmartCardHsm](SmartCardHSM)
* [Coolkey (RHCS)](Coolkey)

## USB Tokens

Each entry on this list possibly represents a whole family of tokens. See each page to find out which models are supported. These devices are also known as cryto-sticks.

* [Aktiv Co. Rutoken ECP](Aktiv-Co.-Rutoken-ECP)
* [Aktiv Co. Rutoken S](Aktiv-Co.-Rutoken-S)
* [Aladdin Etoken Pro](Aladdin-eToken-PRO)
* [Athena ASEPCOS / ASEKey](Athena-ASEPCOS-ASEKey)
* [CardContact SmartCardHsm](SmartCardHSM)
* [Crypto Stick](OpenPGP-card)
* [Feitian ePass2003](Feitian-ePass2003)
* [Feitian ePass3000](Feitian-ePass3000)
* [Feitian PKI token](Feitian-ePass-PKI-token)
* [Nitrokey Pro, Start, Storage](OpenPGP-card), [HSM](SmartCardHSM)
* [Schlumberger / Axalto e-gate](Schlumberger-Axalto-Gemalto-e-gate)
* Rainbow iKey-3000

**Did not find your card from the supported card list?** See [FrequentlyAskedQuestions](Frequently-Asked-Questions) for next steps.

## Unsupported hardware

Things that we have (some) code for but which are known to be incomplete, broken or largely useless.

Unclear/unsupported eID cards:

* Australian national ID card
* Finnish eID
* Taiwan eID
* Swedish eID
* [Belgian eID](Belgian-Belpic)
* [German EGK](German-eHBA,-eGK)
* Malaysian MyKAD
* [Spanish eID](Spanish-Ceres-DNIe)
* [Austrian eID](Austrian-"Bürgerkarte")
* Italian Postecert
* [Swedish BankID](Swedish-BankID)

Unsupported USB tokens:

* Rainbow iKey-4000
* [CryptoIdentityItsec](Eutron-CryptoIdentity-ITSEC-I-ITSEC-P)

Unsupported smart cards:

* IBM JCOP
* [EMV](EMV-(Europay,-Mastercard,-VISA))
* [Seccos](Seccos)
* [Actalis](Italian-signature-card-Actalis)
* [ACOS5](ACOS5)
* [GemplusGpk](Gemplus-GPK-16k)
