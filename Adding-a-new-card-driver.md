# Adding a new card driver

## Preconditions when writing a new card driver

- Identify the card. A useful tool is to check the card's ATR (`opensc-tool -a`) against the database located at https://smartcard-atr.apdu.fr/
- Most useful is the developer documentation with the APDUs that the card is capable of processing.
- Even if no documentation is available, it is possible to spy the APDUs which the card accepts using a possibly proprietary card driver (e.g. PKCS#11 module).
- To receive the debug log of pcscd on Linux (including APDUs), you can run `pcscd` in the foreground with debugging enabled:
  ```bash
  sudo pcscd --foreground --debug --apdu
  ```
  Or, if managed by systemd, you can configure `PCSCD_ARGS="--debug --apdu"` in `/etc/default/pcscd` (or via a systemd drop-in) and view logs via:
  ```bash
  journalctl -u pcscd --follow
  ```
- To watch APDUs on macOS, you can enable smart card logging and use the `log` command:
  ```bash
  sudo defaults write /Library/Preferences/com.apple.security.smartcard Logging -bool yes
  log stream --predicate '(subsystem == "com.apple.CryptoTokenKit") && (category == "APDULog")'
  ```
- On Windows, you can use the [APDUTrace](https://www.mysmartlogon.com/download#APDUTrace) tool. Refer to the user documentation at https://www.mysmartlogon.com/download#APDUTrace

## Card driver programming

### Investigation

If (some previous version of) the card already has a driver in OpenSC, it may only be needed to add the ATR map to your new card and change some minor details to the card driver. Most cards are based on the ISO/IEC 7816 Specification, which is used by most card drivers in OpenSC as base implementation for card driver specific modifications. Ideally, you keep changes to OpenSC small and managable. Only when your card deviates from some existing card-driver implementation too much, create a new card driver

### Creating a new card driver

Basic tasks to hook up a new driver to the OpenSC framework:

1. create `card-example.c` (based on the structure of some existing driver),
2. add `example` to the list of `internal_card_drivers` in `ctx.c`,
3. add `extern sc_card_driver_t *sc_get_example_driver(void);` to `cards.h`,
4. add `card-example.c` to the end of the lists in `Makefile.am` and `Makefile.mak`,
5. re-create Autotools scripts: `./bootstrap`.

Creating the skeleton card driver:

1. identify any card revisions, to be included in ATR map etc,
2. add to the end of enum list in cards.h (+1000 base).

PKCS#15 driver hookup:

* PKCS#15 card formats should need minimal or no modifications, to allow `sc_pkcs15_bind` to scan the card and populate in-memory structures,
* non-PKCS#15 cards need to create a `pkcs15-example.c`, hook it to `builtin_emulators` list in `pkcs15-syn.c` and add to lists in `Makefile.am` and `Makefile.mak`,
* `pkcs15-example.c` creates the in-memory structure by linking right objects with their counterparts based on ID codes or whatever information that is necessary.

### AI Assistance

Even if no detailed documentation is available for a new card, AI tools like ChatGPT, Gemini or Claude can be of significant help. Feed it with your card's documentation, debug logs and let the assistant figure out if it resembles some existing card driver or if a new one is needed. Often it is enough to only modify basic commands like file reading or signature creation to get a working example.

### Basic card driver testing

Use OpenSC's command line tools to verify your implementation:

* `opensc-tool -n` shows the name of the card
* `pkcs15-tool -D` dumps the card's profile with certificates, keys and PINs
* `pkcs11-tool --test --login` performs basic signature validation tests with the card

For more elaborated tests, see [Smart Card Release Testing](Smart-Card-Release-Testing).

### Windows `minidriver` support

Microsoft Windows probes the Smart Card security drivers and loads it related features thanks to the ATRs that should registered (see regedit) from the minidriver framework.
You should carefully register the ATRs using the [OpenSC installer customization](https://github.com/OpenSC/OpenSC/blob/master/win32/customactions.cpp) based on the [following rules](https://docs.microsoft.com/en-us/windows/win32/api/winscard/nf-winscard-scardintroducecardtypea).

#### `pbAtrMask` bitmask

Optional bitmask to use when comparing the ATRs of smart cards to the ATR supplied in `pbAtr`.
If this value is non-NULL, it must point to a string of bytes the same length as the ATR string supplied in `pbAtr`.
When a given ATR string A is compared to the ATR supplied in `pbAtr`, it matches if and only if `A & M = pbAtr`, where `M` is the supplied mask, and `&` represents bitwise `AND`.

## Examples

* [New card driver: EnterSafe card example](New-card-driver-EnterSafe-card-example)
