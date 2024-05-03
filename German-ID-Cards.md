# German ID Cards

Germany has several laws for smart cards. Until 2006 most ID cards conforming to those laws were using the [TCOS-based-preformatted-cards](TCOS-based-preformatted-cards) 2.0X card operating
system. One exception was the 1024bit D-Trust card which was Micardo based.

## History

Until the end of 2007 the German government (i.e. the Bundesnetzagentur) required a minimal key length of 1024 bit. Since the beginning of 2008/2009 this requirement was raised to 1280/1536 bit. Therefore all German trust centers now offer 2048 bit cards. As of May 2011 2048 bit fulfills Bundesnetzagentur-requirements at least until 2017.

The German government was using the RIPEMD160 hash algorithm within their 1024 bit root-certificates ignoring the fact that the rest of the world was using MD5, SHA-1 or SHA-256 instead. One consequence was that you were not able to store the RIPEMD160-based German 1024bit root certificate within the trusted keystore of almost all popular signature aware products like IE, Outlook, Mozilla, Thunderbird, Acrobat. This changed when the key length of the German root certificates was increased from 1024 bit to 2048 bit. Since then the Bundesnetzagentur uses SHA-512 within their 2048 bit root-certificates (12R-CA 1:PN and 13R-CA 1:PN, valid from May 25th 2007 until May 25th 2012).

Since July 2008 German signature cards must not use SHA-1 anymore but must use RIPEMD160, SHA-224, SHA-256, SHA-384 or SHA-512. This forced some trust center to replace all of their signature card in the middle of 2008 (of course after they had replaced all of their signature cards at the beginning of 2008 due to the increased key length). Since 2010 RIPEMD160 is not allowed anymore.

As of May 2011 you may get signature cards from the following Trust centers in Germany:

* Deutsche Telecom AG (!TeleSec GmbH) (akkreditiert seit 22.12.1998).
* Datev AG (akkreditiert seit 9.3.2001, Zertifikatsausgabe nur an Steuerberater, Rechtsanwälte und Wirtschaftsprüfer).
* D-Trust GmbH (akkreditiert seit 8.3.2002).
* Deutsche Post (akkreditiert seit 17.9.2004).
* TC Trust Center GmbH (akkreditiert seit 24.5.2006).
* DGN Deutsches Gesundheitsnetz Service GmbH (akkreditiert seit 9.8.2007).
* medisign GmbH (akkreditiert seit 28.8.2008)
* Deutscher Sparkassen Verlag GmbH (akkreditiert seit 12.11.2008).

## TeleSec, NetKey cards

TeleSec GmbH is the manufacturer of cards and they offer [TCOS](TCOS-based-preformatted-cards) based signature cards, i.e. NetKey E4 cards. Until the end of 2007 theses card were TCOS2 based with a maximal key length of 1024 bit. Since October 2007 TeleSec offers 2048 bit signature cards which are TCOS3 based.

TCOS2 cards work well with OpenSC 0.10.0 or later. There was a problem in 0.12.0 and 0.12.1 which as fixed in 0.12.2 (ticket #256). TCOS3 support was added in December 2007 and is included in OpenSC 0.11.5. Unfortunately the 2048 bit NetKey card contains one key (the one that conforms to the German signature law) that can be used only over a secure channel. So if you want to use this particular key with OpenSC you must wait until OpenSC supports Secure Messaging.

OpenSC provides `netkey-tool` for working with Netkey E4 cards.
You will find more information about NetKey cards on a [separate wiki page on TCOS based cards](TCOS-based-preformatted-cards).

## Deutsche Post, SignTrust card

1024 bit SignTrust cards are [TCOS-based-preformatted-cards](TCOS-based-preformatted-cards) 2.0 based. They work well with OpenSC.

When Deutsche Post replaced their 1024bit cards with 2048bit cards they changed the card operating system from TCOS 2.0 to [STARCOS-cards](STARCOS-cards) 3.0. This card operating system is not supported by OpenSC yet. Also early 2048bit SignTrust cards only supported SHA-1 and RIPEMD160 so in order to create signatures that conform to the German signature law one had to use RIPEMD160 with these cards. Since 2010 SignTrust cards use SHA-256.

The qualified signature certificate on a 2048bit SignTrust is signed by a CA-certificate from Deutsche Post which itself was signed by a 2048 bit German root certificate (12R-CA 1:PN). All other certificates on a SignTrust card are signed by a CA-certificate that Deutsche Post signed with a self generated root certificate.

## D-Trust

1024 bit signature cards from D-Trust are Micardo based and were successfully tested with OpenSC 0.11.1. 2048 bit D-Trust cards are [Siemens-CardOS-M4](Siemens-CardOS-M4) 4.3 based. D-TRUST cards 2.0 2cc conform to the PKCS#15 standard and work well with OpenSC 0.11.4. D-Trust uses strange IDs though. Here's some demo output:

```bash
$ pkcs15-tool -r 000102030405060708090a0b0c0d0e0f | openssl x509 -noout -text -certopt no_pubkey,no_sigdump
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 234973 (0x395dd)
        Signature Algorithm: sha1WithRSAEncryption
        Issuer: C=DE, O=D-Trust GmbH, CN=D-TRUST Qualified CA 1 2006:PN
        Validity
            Not Before: Jul 25 10:20:31 2007 GMT
            Not After : Aug  4 10:20:31 2009 GMT
        Subject: C=DE, CN=Peter Koch, GN=Peter, SN=Koch/serialNumber=DTRWE181908128430122
        X509v3 extensions:
            X509v3 Authority Key Identifier:
                keyid:84:20:88:7F:C1:8F:53:45:C0:3B:B3:7F:F4:B5:53:3B:73:59:CC:84
            Authority Information Access:
                OCSP - URI:http://qual.ocsp.d-trust.net
            X509v3 Certificate Policies:
                Policy: 1.3.6.1.4.1.4788.2.30.1
            X509v3 CRL Distribution Points:
                URI:http://www.d-trust.net/crl/d-trust_qualified_ca_1_2006.crl
            X509v3 Issuer Alternative Name:
                email:info@d-trust.net, URI:http://www.d-trust.net
            X509v3 Subject Key Identifier:
                88:66:AB:03:C0:DE:72:D6:5D:57:9A:D7:14:69:59:B3:BD:BD:9E:47
            X509v3 Key Usage: critical
                Non Repudiation
```

Here's what D-Trust told me on 2008 CeBIT (sorry, but I cannot translate this, I'm not even sure whether I understand it):

```text
D-Trust ist ein akkreditierter Zertifizierungsdiensteanbieter. Die Akkreditierung bezieht sich auf D-Trust selber, nicht auf die von D-Trust angebotenen Produkte. Es gibt prinzipiell keine akkreditierten Produkte, sondern nur akkreditierte Zertifizierungsdiensteanbieter. Die Annahme, dass alle qualifizierten Signaturkarten eines akkreditierten Zertifizierungsdiensteanbieter auch aus dem Trust-Center stammen, für das der Zertifizierungsdiensteanbieter akkreditiert wurde, ist falsch. Ein akkreditierter Zertifizierungsdiensteanbieter kann vielmehr auch weitere Trust-Center betreiben und als akkreditierter Zertifizierungsdiensteanbieter Signaturkarten vertreiben, die aus diesen anderen Trust-Centern stammen. Genau das macht D-Trust: Es betreibt zusätzlich zum Trust-Center, das sich im akkreditierten Betrieb befindet, ein weiteres Trust-Center und aus diesem Trust-Center stammen die qualifizierten Signaturkarten. Qualifizierte Signaturkaten aus dem im akkreditierten Betrieb befindlichen Trust-Center sind nicht allgemein verfügbar."
```

## Sparkassenverlag, S-Trust card

Sparkassenverlag is another trust center in Germany.

OpenSC does not support the S-Trust card of Sparkassenverlag. There cards are [SECCOS](SECCOS) based, and can also contain 'Geldkarte' and 'HBCI' Applications. They are comparably inexpensive, my card was €9, plus 'qualified certificate' at about €20 per year.

## TC Trust Center

No information provided.

## DGN, Medisign

DGN produces several types of smartcards. All types are based on STARCOS 3.2 (RSA 2048 bit, SHA-256 or SHA-512). Some types provide multisignature feature up to 254 signatures.
DGN is also specialized in healthcare market and produces health professional cards. These cards are sold via medisign.

## Datev

Datev had a trust center in Germany that was closed in 2007. Their 1024 bit cards were [TCOS-based-preformatted-cards](TCOS-based-preformatted-cards) 2.0 based.
