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

## Decoding the identity data objects

`pkcs15-tool -D` prints the objects as raw bytes, and `-R <label> -o <file>`
writes a single one to a file. Each object is a BER-TLV wrapper (tag, length,
value), so getting the human-readable content means stripping the header and
decoding the value:

| Object label on card | Path | Tag | Length form | Value |
|---|---|---|---|---|
| Numero de documento | `7001` | `5F 01` | short, 1 byte | ASCII digits |
| Datos biograficos | `7002` | `1F xx`, one per field | short, 1 byte | UTF-8 text |
| Fotografia | `7004` | `3F 01` | **long**, `81`/`82` + N bytes | JPEG |
| MRZ | `700B` | `7F 01` | short, 1 byte | ASCII, fixed-width lines |

Only the photo exceeds 255 bytes, so it is the only object using a long-form
length. These lengths were obtained by reading official documentation and experimentation. **Always read the length byte instead of assuming a fixed header size**.

### Document number (`7001`)

Tag `5F 01`, one length byte, then that many ASCII digits, so the value starts at
offset 3.

This is a different field from the ID number in `7002` (tag `1F 07`); the two
usually differ in length and are not interchangeable.

### Biographic data (`7002`)

A flat run of TLVs, each with a two-byte tag `1F xx` and a one-byte length. Walk
the buffer from offset 0: read the tag and length, take that many bytes as the
value, advance by 3 + length, and stop once the byte at the current offset is no
longer `1F` (end of data or padding) or a declared length would overrun the
buffer.

Zero-length fields are normal, only the header is present. The text is **UTF-8**,
not ASCII, decoding it as latin-1 gives mojibake on accented names.

| Tag | Field | Format |
|---|---|---|
| `1F 01` | Surname(s) | UTF-8 text |
| `1F 02` | Second surname | UTF-8 text, might be empty |
| `1F 03` | Name(s) | UTF-8 text, all given names in one field |
| `1F 04` | Nationality | ISO 3166-1 alpha-3 (e.g. `URY`) |
| `1F 05` | Date of birth | `DDMMYYYY` |
| `1F 06` | Place of birth | `CITY/COUNTRY`, country as alpha-3 |
| `1F 07` | ID number | digits in ASCII, check digit last |
| `1F 09` | Expiry date | `DDMMYYYY` |

Cards have also been seen carrying `1F 08` and `1F 0A`, with no documented
meaning. Surface unknown tags rather than dropping them.

> **The surname split is not reliable.** The layout puts the second surname in
> `1F 02`, but cards have been observed writing *both* surnames into `1F 01` and
> leaving `1F 02` zero-length. An empty `1F 02` therefore does not mean the holder
> has a single surname, and splitting `1F 01` on whitespace is ambiguous with
> compound surnames (*De León*, *Da Silva*). Label `1F 01` as *surname(s)*.

### Photo (`7004`)

Tag `3F 01`, followed by a long-form length. The byte at offset 2 gives the
header size: below `0x80` it is the length itself and the value starts at offset
3, `0x81` means one more length byte and the value starts at 4, `0x82` means two
and the value starts at 5. Portraits are a few tens of kilobytes, above the
255-byte ceiling of `0x81` and below the 65535 of `0x82`, so `0x82` is the usual
case.

The value is a complete JPEG, no re-encoding needed: write it out as-is. It
should start with `FF D8 FF` (SOI) and end with `FF D9` (EOI); if it does not,
the length form was misread and the value offset is wrong.

### MRZ (`700B`)

Tag `7F 01`, one length byte, then that many ASCII characters starting at offset
3. The value carries no line separators, so split it positionally by total
length: 90 characters is TD1 (3 lines of 30, the ID-card format), 88 is TD3
(2 lines of 44, the passport format). Trailing runs of `<` are MRZ filler, not
data.

## Notes

* The ATR differs between card batches (the applet version and batch bytes), so
  the driver matches it with a mask.
* The identity data objects are readable without authentication by design, since
  the same data is printed on the card itself. Anything read out of them is still
  personal data, treat it accordingly.
