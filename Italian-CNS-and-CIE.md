# Italian CNS, CIE and digital signature

Italy has an unusually large number of smart cards that all call themselves "CNS", plus an
electronic identity card (CIE) that looks related but is not the same platform. This page explains
what those names mean, how to tell which card you are holding, and what OpenSC can and cannot do
with it.

## 1. CNS is a specification, not a product

**CNS** stands for *Carta Nazionale dei Servizi* (National Service Card). It is not a card model:
it is a set of published specifications — a file system layout, an APDU command set and an X.509
authentication certificate profile — that any Italian public administration may implement. The
administration that issues a card (*ente emettitore*) is required to be a public body, but the
technical rules explicitly allow it to delegate the actual work to third parties: a **manufacturer**
initialises and personalises the cards, and a **qualified trust service provider** issues the
certificates. Each *ente emettitore* runs its own procurement.

This is the reason for the fragmentation: every Region, Chamber of Commerce, professional body or
ministry that issues CNS cards has picked its own chip, its own manufacturer and its own CA, and
each procurement round changed them again. All of those cards are legitimately "a CNS".

Cards you are likely to meet:

* **TS-CNS** — the national health card with a chip on the back, mailed free of charge by the
  Ministry of Economy and Finance on expiry of the previous one. It carries **only** the CNS
  authentication certificate. Since 1 June 2022 the health card may also be issued *without* a
  chip, in which case it is not a CNS at all.
* **Regional service cards** (CRS, *carta regionale/provinciale dei servizi*) — predecessors of the
  TS-CNS, issued by some Regions and autonomous Provinces; Lombardy's CRS started before the CNS
  regulation existed.
* **Chamber of Commerce CNS** — issued to company representatives; carries a CNS certificate **and**
  a qualified signature certificate.
* **Professional-body CNS** — issued by national councils (architects, accountants, notaries, ...),
  usually with a qualified signature certificate and sometimes a printed photograph.
* **Commercial CNS** — sold by trust service providers on behalf of a public *ente emettitore*,
  typically as a bundle of a CNS certificate plus a qualified signature certificate, on a smart card
  or a USB token.

**CIE** stands for *Carta d'Identità Elettronica* (Electronic Identity Card). It is today the
principal identity document in Italy: since 2016 it has been replacing the paper identity card, and
its primary purpose is to prove the holder's identity in person. Unlike the CNS — which is
essentially a set of certificates — the CIE is first of all a physical identity document; as an
added feature it carries a single authentication certificate. It can be used to sign documents, but
the result is only an advanced electronic signature (*firma elettronica avanzata*), never a qualified
one; and, as with the CNS, its use substitutes an advanced electronic signature towards the public
administration for the purposes of arts. 64–65 of the Digital Administration Code (DPCM 22 February
2013, art. 61 c. 2). It is issued only by the Ministry
of the Interior (municipalities act as registration authorities), and a citizen holds at most one.
The first two generations (2001–2004 pilots) are contact cards, CNS-like, and are handled by OpenSC;
the current **CIE 3.0** (2016 onwards) is a different, **contactless-only** (NFC) platform and is
*not* supported — see section 5.

A note on a claim that used to be on this page: there is **no rule forbidding a CNS to a CIE
holder**. The technical rules (DM 9 December 2004, §4.1.2) only require the citizen to *declare*, at
registration, that they do not hold a CIE; the preventive check that once existed (DPR 117/2004,
art. 8 c. 5) was limited to the CIE pilot municipalities and to 31 December 2005, and was repealed
in 2009. In practice everybody holds both.

## 2. Two certificates that do different things

Most confusion about these cards comes from mixing up the two certificates.

### The CNS authentication certificate

Its profile is fixed by AgID and is easy to recognise:

* `Key Usage`: `digitalSignature`, marked critical — and **`nonRepudiation` must not be set**; other
  bits may be present, `keyEncipherment` is common;
* `Extended Key Usage`: must contain `clientAuth` (`1.3.6.1.5.5.7.3.2`); further values are allowed
  and usual, e.g. Microsoft Smartcard Login and E-mail Protection;
* `Certificate Policies`: must contain the OID **`1.3.76.16.2.1`** with the explicit text
  *"Identifies X.509 authentication certificates issued for the italian National Service Card (CNS)
  project in according to the italian regulation"*. Lombardy's CRS/SISS cards use
  `1.3.159.6.1.3.2.10` or `1.3.76.12.1.1.10.2.2.10`, which the same document declares equivalent;
* `commonName`: `<fiscal code>/<card id>.<hash of the personal-data EF>`;
* `organizationalUnitName`: the name of the issuing administration.

That policy OID is the reliable way to answer *"is the certificate on my token a CNS certificate?"*:

```console
$ pkcs15-tool --read-certificate 01 | openssl x509 -noout -text
...
        Subject: CN = "<fiscal code>/7430010024925855.lsKV/lNtBAmc8x5lzSb+cxx2UzE=",
                 OU = Universita' della Calabria, C = IT
...
                Policy: 1.3.76.16.2.1
                  User Notice:
                    Explicit Text: Identifies X.509 authentication certificates issued for the
                    italian National Service Card (CNS) project in according to the italian regulation
...
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage:
                TLS Web Client Authentication, Microsoft Smartcard Login, E-mail Protection
```

The card-id part of the `commonName` is the same value `pkcs15-tool --dump` reports as the token
serial number, which is a handy cross-check that you are looking at the right card.

The certificate is used for TLS client authentication against Italian public-administration portals.
It is **not**
a qualified certificate, so a signature made with it is not a *firma digitale*: Italian signature
verification tools will report that the certificate has no legal value for signing. Italian law does
give it a specific, narrower effect — using the CNS substitutes an advanced electronic signature
*towards the public administration*, for the purposes of arts. 64–65 of the Digital Administration
Code (DPCM 22 February 2013, art. 61 c. 2) — but it never substitutes a qualified signature.

### The qualified signature certificate

Where present, it is a **separate certificate on a separate key**, with `Key Usage:
nonRepudiation`, usually protected by its **own PIN**, and often living in a different application or
DF on the card. This is the one that produces a legally binding *firma digitale*.

Practical consequence: a TS-CNS lets you log in to public portals, but it cannot sign — for example
filings to the Business Register require a *firma digitale* by law (art. 31 c. 2 L. 340/2000). If you
need to sign, you need a card that carries a qualified signature certificate.

The reverse is also worth knowing: several non-PA systems (regulated energy markets, for instance)
require a client-authentication certificate on a device whose profile is exactly the CNS profile,
which is why new CNS cards are still being issued and sold today even though citizens can use SPID
or CIE to log in to public services.

## 3. Which card do I have?

```console
$ opensc-tool --atr          # raw ATR
$ opensc-tool --name         # driver and card type as detected
$ pkcs15-tool --dump         # certificates, keys, PINs and data files
```

For a classic CNS the ATR is the giveaway: OpenSC's `itacns` driver accepts any card whose ATR has
15 historical bytes with the ASCII string **`CNS`** (`43 4E 53`) at offset 9, and reads the CNS
version from byte 12 (`10` = 1.0, `11` = 1.1). For example:

```
3B FF 18 00 00 81 31 FE 55 00 6B 02 09 07 03 01 01 01 43 4E 53 10 31 80 66
                           |-- 15 historical bytes ------------------|
                                 ^^^^^                ^^^^^^^^ ^^
                                 IC, mask             "CNS"    v1.0
```

On that card `opensc-tool --name` answers `CNS card` and `pkcs15-tool --dump` reports
`IC: STMicroelectronics; mask: STIncard`: historical bytes 2 and 3 identify the chip and the mask
manufacturer (STMicroelectronics, Infineon, ATMEL, ..., and Athena, IDEMIA (Oberthur), STIncard,
Siemens, Gemalto, ... respectively). Roughly the same information is printed on the plastic of a
TS-CNS as a short code near the TS logo — `AT 2012`, `AC 2013/2014/2018`, `ACx/ACe 2021`,
`ACx/ACe/ACj 2025`, `OT 2015/2016`, `ID 2019`, `ST 2021/2022` — which is the code the issuer's own
driver-download pages are indexed by.

Beware that **the generic CNS ATR is shared by cards that are not classic CNS at all**: some tokens
advertise `CNS` in the ATR but expose their objects through an IAS-ECC application with secure
messaging and vendor-specific containers. OpenSC handles this by ordering the more specific drivers
before `itacns` and having them probe for their application before claiming the card.

## 4. What OpenSC gives you

### `itacns` — classic CNS, CIE v1 and CIE v2

The `itacns` driver plus its PKCS#15 emulation is the workhorse. It exposes the card's data files
(`EF_DatiPersonali`, `EF_IDCarta`, ...), the PIN and the PUK, and then probes four well-known keyset
layouts, adding whatever it actually finds:

| Object label | Certificate path | Notes |
|---|---|---|
| `CNS0` | `3F00 1100 1101` | the CNS authentication certificate; PIN reference `0x10` |
| `CNS01` | `3F00 2FFF 8228` | Infocamere 1204 layout, certificate wrapped in a container |
| `CNS1` | `3F00 1400 9010` | qualified signature keyset, own PIN (reference `0x1a`) |
| `CNS1` | `3F00 1400 9001 2002` | IDEMIA 2021 cards; despite the label this is their *authentication* certificate, which those cards moved into the signature DF — note the PIN reference is `0x10` |

Key usage flags are taken from the certificate's `keyUsage`/`extendedKeyUsage` when OpenSC is built
with OpenSSL, so a `nonRepudiation` signature key is exposed as such. The PKCS#15 object IDs you see
(`01`, `02`, `10`, `21`) are the card's security-environment numbers. RSA-1024 is assumed for CNS
version 1.0 cards, RSA-2048 and extended APDUs for version 1.1 and for IDEMIA 2021.

Note that the emulation adds a keyset **only if it can read and parse the X.509 certificate**: a key
is never exposed without its certificate.

Cards for which the authentication side is known to work, with the change that made them work:

| Card | OpenSC |
|---|---|
| CNS v1.0, RSA-1024 (e.g. `AT 2012`, `AC 2013/2014/2018`, `OT 2015/2016`) | supported since the driver was written (2010) |
| CNS v1.1, RSA-2048 (e.g. `ST 2021`) | [PR #2371](https://github.com/OpenSC/OpenSC/pull/2371), 0.22.0 |
| IDEMIA/Oberthur 2021 (e.g. `ACe/ACx 2021`) | [PR #2483](https://github.com/OpenSC/OpenSC/pull/2483), 0.23.0 |
| CIE v1 (Siemens) and CIE v2 | supported since 2010; *not* CIE 3.0, see below |

### The signature side usually does not show up

**On cards that carry a qualified signature certificate, OpenSC normally shows only the CNS
authentication certificate.** This is not a bug in a specific driver, it follows from the
specifications:

* the CNS file system standardises the *existence* of the signature DF (`DF_DS` = `1400`) but not its
  contents, and the APDU specification treats the digital-signature commands as optional
  (*Annex C — optional commands for digital signature*);
* since the 2016 revision, the file system specification explicitly allows the signature to live in a
  **dedicated Java applet instead of `DF_DS`**, "in alternativa alla modalità classica che prescrive
  l'uso di un file dedicato (DF/DS)", and in that case `DF_DS` "deve comunque essere presente,
  sebbene non ospiti le informazioni necessarie alla firma digitale" — it must exist but stay empty.
  The applet interface is not standardised: the specification only requires the vendor to ship free
  middleware for it.

So the `CNS1` paths in `itacns` describe the classic file-based layout, and cards that use a
signature applet — or that keep the certificate in a vendor-specific container, e.g. a BER-TLV
wrapper around a compressed stream — expose nothing there. No signature certificate found means no
signature key either. There is currently **no Italian CNS documented in OpenSC's history as having a
working signature keyset**; the reports that exist are of the opposite
([#2782](https://github.com/OpenSC/OpenSC/issues/2782),
[#3479](https://github.com/OpenSC/OpenSC/issues/3479), both with the vendor middleware showing a
second certificate that OpenSC does not). A common case is cards manufactured by Bit4id, whose
middleware labels the signature object `DS3` or `DS4` while OpenSC lists only `CNS0`. The card whose
ATR is shown in section 3 behaves exactly that way: it does carry a qualified signature certificate,
and `pkcs15-tool --dump` lists one PIN, one PUK, one key pair and one certificate, all labelled
`CNS0`.

Consequences for a user:

* if `pkcs15-tool --dump` lists a single certificate labelled `CNS0`, your card may still hold a
  qualified signature certificate that OpenSC cannot see — check with the issuer's middleware before
  concluding it has none;
* signing with OpenSC on such a card will use the CNS authentication key, which produces a signature
  with no legal value as a *firma digitale* (see section 2). Signature verification tools will say so.

Adding support for one of these signature applets is per-vendor work; see
[PR #3751](https://github.com/OpenSC/OpenSC/pull/3751) for a worked example.

### `cardos` + the `actalis` emulation — legacy Actalis signature cards

This covers a **pre-CNS** family: Actalis-issued CardOS signature cards, possibly carrying the
customer's brand, whose serial number starts with the letter `H` followed by digits. The emulation
reads the serial from `3F00 3000 0001`, then the zlib-compressed *User Non-repudiation*, *TSA* and
*CA* certificates from `3F00 3000 6000 6002-6004`, and exposes the authentication key from
`3F00 3000 4000 0008`. It requires OpenSC to be built with zlib.

The `actalis` emulation is **not enabled by default**: it is registered among the legacy emulators,
which the default configuration does not try. To use it, set in `opensc.conf`:

```
app default {
    framework pkcs15 {
        builtin_emulators = old, internal;
    }
}
```

Actalis also issues CNS-compatible cards (they may carry Athena's ASEPKCS dedicated file but speak
the CNS command set); those are handled by `itacns` or `asepcos`, not by this emulation.

### Bit4id Digital-DNA Key (NXP ChipDoc) — support in progress

A CNS/eID built on an NXP ChipDoc chip, gated behind a PACE (BSI TR-03110) channel and storing its
certificates in a vendor-specific compressed container. A `chipdoc-it` driver with PKCS#15 emulation
exposing the CNS certificate, the qualified signature certificate (DS3) and both RSA keys is
proposed in **[PR #3751](https://github.com/OpenSC/OpenSC/pull/3751)** — not merged yet, so it is
not in any release.

### Contactless (NFC) — support in progress

Many CNS cards carry a dual-interface chip and can also be read over NFC. Over the contactless
interface the card answers a bare ATR `3B 80 80 01 01` with no historical bytes, so `itacns` — which
recognises a CNS by the `CNS` marker in the historical bytes — cannot identify it, and the card is
reported as `CKR_TOKEN_NOT_RECOGNIZED` even though the *same card works over contact*. This is what
was seen with an `ST 2022` health card ([#3755](https://github.com/OpenSC/OpenSC/issues/3755)): it is
supported over the contact interface, and only the NFC path was missing.

A change that identifies the card over NFC by selecting its application instead of by ATR is proposed
in **[PR #3806](https://github.com/OpenSC/OpenSC/pull/3806)** — not merged yet.

### Not supported

* **CIE 3.0** (the electronic identity card issued since 2016). It is IAS-ECC based with a privacy
  protocol on top, and a proposal to support it was closed as not planned
  ([#2486](https://github.com/OpenSC/OpenSC/issues/2486)). Use the official
  [CIE middleware](https://github.com/italia/cie-middleware) instead.
* **The Actalis `ACe 2025` CNS**, recognised as a CNS but with its certificate at a card-specific
  path that `itacns` does not yet read; being worked out in
  [#3804](https://github.com/OpenSC/OpenSC/issues/3804) (open).
* **The qualified signature function of most cards that have one**, as explained above.

If your card is not supported, the health-card system publishes a driver finder indexed by the code
printed on the card, and the middleware it points to (bit4id xpki, Cyberneid, SafeDive) exposes a
PKCS#11 module you can use instead of OpenSC.

## 5. References

Specifications (AgID):

* [Carta Nazionale dei Servizi — specifications hub](https://www.agid.gov.it/it/piattaforme/carta-nazionale-servizi)
* [CNS card operating system (APDU), v1.1.6](https://www.agid.gov.it/sites/default/files/repository_files/documentazione_trasparenza/cns_functional_specification_1.1.6_02042011.pdf)
* [CNS file system, v11 (2024)](https://www.agid.gov.it/sites/default/files/repository_files/filesystemcns_2024_01_29_v.11.pdf)
* [CNS authentication certificate profile](https://www.agid.gov.it/sites/default/files/repository_files/documentazione_trasparenza/strutturacertificatoautenticazionecns_v1.1_.pdf)
* [DM 9 December 2004 — technical rules](https://www.agid.gov.it/sites/default/files/repository_files/documentazione_trasparenza/decreto_9_dicembre_2004.pdf)
* [CIE developer documentation](https://developers.italia.it/en/cie/)

Drivers and middleware for cards OpenSC does not support:

* [TS-CNS driver finder, by card code (Sistema Tessera Sanitaria)](https://sistemats1.sanita.finanze.it/portale/elenco-driver-cittadini-modalita-accesso)
* [CIE middleware](https://github.com/italia/cie-middleware)
