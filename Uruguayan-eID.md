# Uruguayan eID (cédula de identidad)

OpenSC supports the Uruguayan national electronic ID card (*cédula de identidad
electrónica*) through the dedicated `cedulauy` card driver. The card is a
Gemalto/Thales IAS Classic platform, exposed as a read-only synthesized PKCS#15
card, so it works with the PKCS#11 provider on Linux, macOS and Windows with no
card-specific configuration.

Two applet versions are in circulation, both supported over the contact
interface:

* **IAS Classic v4** (2015 chip, contact only), which AGESIC labels a *hybrid*
  card.
* **IAS Classic v5** (2022 chip, MultiApp V5.0), which AGESIC labels a
  *dual-interface* card. It adds a contactless/NFC interface with PIN
  verification over PACE.

Cards issued from late 2022 are the v5, earlier ones are the v4. The card reports
its own version in the applet label, read with `GET DATA` (tag `7F30`, object
`C0`): the ASCII string is either `IAS Classic v4` or `IAS Classic v5`. OpenSC
does not need to tell them apart, the driver matches both batches with a single
masked ATR (the version and batch bytes are masked out).

The card is issued by the Dirección Nacional de Identificación Civil (DNIC) of
the Ministry of the Interior. The surrounding digital-identity infrastructure is
run by [AGESIC](https://www.gub.uy/agencia-gobierno-electronico-sociedad-informacion-conocimiento).

> **Note:** support is currently in the `master` branch and will ship in the
> next OpenSC release. Until then, build OpenSC from source.

Resources:

* Official digital-identity information: [AGESIC](https://www.gub.uy/agencia-gobierno-electronico-sociedad-informacion-conocimiento).
* [firmauy](https://github.com/carlosplanchon/firmauy): a third-party command-line
  tool to sign and verify PDF (PAdES), XML (XAdES) and arbitrary files
  (CAdES/.p7s) with the cédula over PKCS#11, with local certificate-chain
  validation up to the Uruguayan national root.

## Card capabilities

* **Interface:** contact (ISO 7816) on both v4 and v5. The contactless/NFC
  interface (PIN over PACE), present only on the v5 (2022) card, is not supported
  yet.
* **Signing:** a single RSA-2048 key with *sign* and *non-repudiation* usage. The
  signature is computed on-card (`MSE:SET DST`, then `PSO:HASH` and `PSO:CDS`),
  with SHA-256 as the algorithm used in practice.
* **Certificate:** the signing certificate is readable without a PIN.
* **Identity data:** the document number, biographic data, cardholder photo and
  MRZ are exposed as PKCS#15 data objects, readable without a PIN.
* **PIN:** a single global user PIN protects the signing key.

## Example

Reading the card structure with `pkcs15-tool` (cardholder name, serial number and
the identity data-object contents redacted, they are personal data):

```console
$ pkcs15-tool -D
Using reader with a card: ACS ACR 38U-CCID 00 00
PKCS#15 Card [CARDHOLDER NAME]:
    Version        : 0
    Serial number  : <redacted>
    Manufacturer ID: (null)
    Flags          :

PIN [PIN]
    Object Flags   : [0x00]
    ID             : 01
    Flags          : [0x30], initialized, needs-padding
    Length         : min_len:4, max_len:12, stored_len:12
    Pad char       : 0x00
    Reference      : 17 (0x11)
    Type           : ascii-numeric

Private RSA Key [Clave de Firma]
    Object Flags   : [0x01], private
    Usage          : [0x204], sign, nonRepudiation
    Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
    Algo_refs      : 0
    ModLength      : 2048
    Key ref        : 1 (0x01)
    Native         : yes
    Auth ID        : 01
    ID             : 01

X.509 Certificate [Certificado de Firma]
    Object Flags   : [0x00]
    Authority      : no
    Path           : a00000001840000001634200::b001
    ID             : 01

Data object 'Numero de documento'
    applicationName: Numero de documento
    Path:            3f0070007001
    Data (12 bytes): <document number, redacted>

Data object 'Datos biograficos'
    applicationName: Datos biograficos
    Path:            3f0070007002
    Data (103 bytes): <names, nationality, dates, place of birth, redacted>

Data object 'Fotografia'
    applicationName: Fotografia
    Path:            3f0070007004
    Data (10164 bytes): <JPEG portrait, redacted>

Data object 'MRZ'
    applicationName: MRZ
    Path:            3f007000700b
    Data (93 bytes): <machine-readable zone, redacted>
```

## Notes

* The ATR differs between card batches (the applet version and batch bytes), so
  the driver matches it with a mask.
* The identity data objects are readable without authentication by design, since
  the same data is printed on the card itself.
