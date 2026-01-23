# Smart Card Release Testing

## Table of Contents

* [Test Results](#test-results)
  * [OpenSC 0.27.0](#opensc-0270)
  * [OpenSC 0.26.0](#opensc-0260)
  * [OpenSC 0.25.0](#opensc-0250)
  * [OpenSC 0.24.0](#opensc-0240)
  * [OpenSC 0.23.0](#opensc-0230)
  * [OpenSC 0.22.0](#opensc-0220)
  * [OpenSC 0.21.0](#opensc-0210)
  * [OpenSC 0.20.0](#opensc-0200)
  * [OpenSC 0.19.0](#opensc-0190)
  * [OpenSC 0.18.0](#opensc-0180)
  * [OpenSC 0.17.0](#opensc-0170)
* [Test Cases](#test-cases)
  * [Installer](#installer)
    * [Installation](#installation)
      * [Windows](#windows)
      * [macOS](#macos)
    * [Removal](#removal)
      * [Windows](#windows)
      * [macOS](#macos)
  * [PKCS#11](#pkcs11)
    * [`pkcs11-tool`](#pkcs11-tool)
      * [Windows](#windows)
      * [Other Operating Systems](#other-operating-systems)
    * [Firefox](#firefox)
      * [Load OpenSC PKCS#11 Module](#load-opensc-pkcs11-module)
      * [PIN Verification](#pin-verification)
      * [TLS Client Authentication](#tls-client-authentication)
    * [OpenSSH](#openssh)
      * [Without ssh-agent](#without-ssh-agent)
      * [With ssh-agent](#with-ssh-agent)
  * [Windows Minidriver](#windows-minidriver)
    * [`certutil -scinfo`](#certutil--scinfo)
    * [Windows Login or Unlock](#windows-login-or-unlock)
    * [TLS Client Authentication with Internet Explorer, Edge or Chrome](#tls-client-authentication-with-internet-explorer-edge-or-chrome)
    * [Change PIN](#change-pin)
  * [macOS Tokend](#macos-tokend)
    * [Keychain Access](#keychain-access)
    * [TLS Client Authentication with Safari or Chrome](#tls-client-authentication-with-safari-or-chrome)
  * [Detaching and attaching reader with card inserted](#detaching-and-attaching-reader-with-card-inserted)

## Test Results

* :full_moon: All tests passed
* :waning_gibbous_moon: Many tests passed
* :last_quarter_moon: Some tests passed
* :waning_crescent_moon: Few tests passed
* :new_moon: Untested

### OpenSC 0.27.0

| Installer    | Windows    | macOS       |
| ------------ | ---------- | ----------- |
| Installation | :new_moon: | :new_moon:  |
| Removal      | :new_moon: | :new_moon:  |

The table below shows a list of all supported card drivers (`opensc-tool --list-drivers`) that have been tested in this release:

| Smart Card Driver    | PKCS#11        | Windows Minidriver | macOS CTK  |
| -------------------- | -------------- | -------------- | -------------- |
| cardos               | :full_moon:    | :new_moon:     | :new_moon:     |
| cyberflex            | :new_moon:     | :new_moon:     | :new_moon:     |
| gemsafeV1            | :new_moon:     | :new_moon:     | :new_moon:     |
| starcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| tcos                 | :new_moon:     | :new_moon:     | :new_moon:     |
| oberthur             | :new_moon:     | :new_moon:     | :new_moon:     |
| authentic            | :new_moon:     | :new_moon:     | :new_moon:     |
| iasecc               | :new_moon:     | :new_moon:     | :new_moon:     |
| belpic               | :new_moon:     | :new_moon:     | :new_moon:     |
| entersafe            | :new_moon:     | :new_moon:     | :new_moon:     |
| epass2003            | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken              | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken_ecp          | :new_moon:     | :new_moon:     | :new_moon:     |
| myeid                | :full_moon:    | :new_moon:     | :new_moon:     |
| dnie                 | :new_moon:     | :new_moon:     | :new_moon:     |
| MaskTech             | :new_moon:     | :new_moon:     | :new_moon:     |
| esteid2018           | :new_moon:     | :new_moon:     | :new_moon:     |
| idprime              | :full_moon:    | :new_moon:     | :new_moon:     |
| coolkey              | :full_moon:    | :new_moon:     | :new_moon:     |
| muscle               | :new_moon:     | :new_moon:     | :new_moon:     |
| sc-hsm               | :full_moon:    | :new_moon:     | :new_moon:     |
| mcrd                 | :new_moon:     | :new_moon:     | :new_moon:     |
| setcos               | :new_moon:     | :new_moon:     | :new_moon:     |
| PIV-II               | :full_moon:    | :new_moon:     | :new_moon:     |
| cac                  | :full_moon:    | :new_moon:     | :new_moon:     |
| itacns               | :new_moon:     | :new_moon:     | :new_moon:     |
| isoApplet            | :new_moon:     | :new_moon:     | :new_moon:     |
| gids                 | :new_moon:     | :new_moon:     | :new_moon:     |
| openpgp              | :full_moon:    | :new_moon:     | :new_moon:     |
| jpki                 | :new_moon:     | :new_moon:     | :new_moon:     |
| npa                  | :new_moon:     | :new_moon:     | :new_moon:     |
| cac1                 | :new_moon:     | :new_moon:     | :new_moon:     |
| nqapplet             | :new_moon:     | :new_moon:     | :new_moon:     |
| skeid                | :new_moon:     | :new_moon:     | :new_moon:     |
| eOI                  | :new_moon:     | :new_moon:     | :new_moon:     |
| default              | :new_moon:     | :new_moon:     | :new_moon:     |

The table below shows a list of all tested smart cards that were used:

| Smart Card Driver | Tested Smart Cards                                               |
| ----------------- | ---------------------------------------------------------------- |
| PIV-II            | YubiKey 5 NFC (fw 5.4.3)                                         |
|                   | ID-One PIV TEST CARD 1, 2, 3, 4                                  |
|                   | Old Nist Test PIV Cards 6, 7, 9, 10, 12, 13, 14, 16              |
|                   | New Nist Test PIV Cards 15, 16                                   |
| openpgp           | YubiKey 5 NFC (fw 5.4.3) with OPENSC_DRIVER=openpgp              |
|                   | Nitrokey start (FSIJ-1.2.10-43245521)                            |
| epass2003         | Feitian ePass2003 (gitlab CI)                                    |
| sc-hsm            | SmartCard-HSM 4K USB-Token (2023, CI)                            |
| coolkey           | test card (CI)                                                   |
| cac               | test card (CI), another test card, Alt token                     |
| idprime           | IDPrime 930, IDPrime 3810, IDPrime 940 (4k RSA, ECC)             |
| cardos            | Cardos 5.3 (2017, 2023)                                          |
| myeid             | Aventra MyEID 4.5


Notes: Recent OpenSSL does not allow SHA1 hashes anymore so the `pkcs11-tool --test` fails
in Fedora 43 now. Running the tests with `OPENSSL_ENABLE_SHA1_SIGNATURES=1` works around
this issue.

### OpenSC 0.26.0

| Installer    | Windows    | macOS       |
| ------------ | ---------- | ----------- |
| Installation | :new_moon: | :new_moon:  |
| Removal      | :new_moon: | :new_moon:  |

The table below shows a list of all supported card drivers (`opensc-tool --list-drivers`) that have been tested in this release:

| Smart Card Driver    | PKCS#11        | Windows Minidriver | macOS CTK  |
| -------------------- | -------------- | -------------- | -------------- |
| cardos               | :full_moon:     | :new_moon:     | :new_moon:     |
| cyberflex            | :new_moon:     | :new_moon:     | :new_moon:     |
| gemsafeV1            | :new_moon:     | :new_moon:     | :new_moon:     |
| starcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| tcos                 | :new_moon:     | :new_moon:     | :new_moon:     |
| oberthur             | :new_moon:     | :new_moon:     | :new_moon:     |
| authentic            | :new_moon:     | :new_moon:     | :new_moon:     |
| iasecc               | :new_moon:     | :new_moon:     | :new_moon:     |
| belpic               | :new_moon:     | :new_moon:     | :new_moon:     |
| entersafe            | :new_moon:     | :new_moon:     | :new_moon:     |
| epass2003            | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| rutoken              | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken_ecp          | :new_moon:     | :new_moon:     | :new_moon:     |
| myeid                | :last_quarter_moon:     | :new_moon:     | :new_moon:     |
| dnie                 | :new_moon:     | :new_moon:     | :new_moon:     |
| MaskTech             | :new_moon:     | :new_moon:     | :new_moon:     |
| esteid2018           | :new_moon:     | :new_moon:     | :new_moon:     |
| idprime              | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| coolkey              | :new_moon:     | :new_moon:     | :new_moon:     |
| muscle               | :new_moon:     | :new_moon:     | :new_moon:     |
| sc-hsm               | :new_moon:     | :new_moon:     | :new_moon:     |
| mcrd                 | :new_moon:     | :new_moon:     | :new_moon:     |
| setcos               | :new_moon:     | :new_moon:     | :new_moon:     |
| PIV-II               | :last_quarter_moon:     | :new_moon:     | :new_moon:     |
| cac                  | :full_moon:     | :new_moon:     | :new_moon:     |
| itacns               | :new_moon:     | :new_moon:     | :new_moon:     |
| isoApplet            | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| gids                 | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| openpgp              | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| jpki                 | :new_moon:     | :new_moon:     | :new_moon:     |
| npa                  | :new_moon:     | :new_moon:     | :new_moon:     |
| cac1                 | :last_quarter_moon:     | :new_moon:     | :new_moon:     |
| nqapplet             | :new_moon:     | :new_moon:     | :new_moon:     |
| skeid                | :new_moon:     | :new_moon:     | :new_moon:     |
| eOI                  | :new_moon:     | :new_moon:     | :new_moon:     |
| default              | :new_moon:     | :new_moon:     | :new_moon:     |

The table below shows a list of all tested smart cards that were used:

| Smart Card Driver | Tested Smart Cards                                               |
| ----------------- | ---------------------------------------------------------------- |
| PIV-II            | NIST Test Card v.1 9 (RSA2048), 4 (ECC), 13 (RSA1024, RSA2048)   |
|                   | NIST Test Card v.2 15 (ECC), 16 (RSA2048)                        |
|                   | Yubikey 4 OTP+U2F+CCID                                           |
|                   | Thales IDPrime PIV v3.0 (detection only)                         |
|                   | PivApplet (JCardSim)                                             |
| myeid             | Aventra MyEID v4.5 PKI card (RSA2045, ECC), Aventra MyEID v4.5.5, OsEID |
| cac               | CAC Test Cards (Oberthur ID One, Gemalto GCX4), virt_CACard      |
| cac1              | CAC test cards (detection only)                                  |
| gids              | GIDS Applet (JCardSim)                                           |
| openpgp           | OpenPGP Applet  (JCardSim), NitroKey Start (gnuk)                |
| isoApplet         | IsoApplet v0 and v1 (JCardSim)                                   |
| cardos            | CardOS 5.3 (2017, 2k RSA), CardOS 5.3 (2023, 4k RSA)             |
| idprime           | IDPrime 940 (RSA & EC), IDPrime 930, IDPrime MD 830, IDPrime 3810 |
| epass2003         | ePass2003 PKI Token (2023)                                       |

### OpenSC 0.25.0

| Installer    | Windows    | macOS       |
| ------------ | ---------- | ----------- |
| Installation | :new_moon: | :new_moon:  |
| Removal      | :new_moon: | :new_moon:  |

The table below shows a list of all supported card drivers (`opensc-tool --list-drivers`) that have been tested in this release:

| Smart Card Driver    | PKCS#11        | Windows Minidriver | macOS CTK  |
| -------------------- | -------------- | -------------- | -------------- |
| cardos               | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| cyberflex            | :new_moon:     | :new_moon:     | :new_moon:     |
| gemsafeV1            | :new_moon:     | :new_moon:     | :new_moon:     |
| starcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| tcos                 | :new_moon:     | :new_moon:     | :new_moon:     |
| oberthur             | :new_moon:     | :new_moon:     | :new_moon:     |
| authentic            | :new_moon:     | :new_moon:     | :new_moon:     |
| iasecc               | :new_moon:     | :new_moon:     | :new_moon:     |
| belpic               | :new_moon:     | :new_moon:     | :new_moon:     |
| entersafe            | :new_moon:     | :new_moon:     | :new_moon:     |
| epass2003            | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| rutoken              | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken_ecp          | :new_moon:     | :new_moon:     | :new_moon:     |
| myeid                | :last_quarter_moon:     | :new_moon:     | :new_moon:     |
| dnie                 | :new_moon:     | :new_moon:     | :new_moon:     |
| MaskTech             | :new_moon:     | :new_moon:     | :new_moon:     |
| esteid2018           | :new_moon:     | :new_moon:     | :new_moon:     |
| idprime              | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| coolkey              | :full_moon:     | :new_moon:     | :new_moon:     |
| muscle               | :new_moon:     | :new_moon:     | :new_moon:     |
| sc-hsm               | :new_moon:     | :new_moon:     | :new_moon:     |
| mcrd                 | :new_moon:     | :new_moon:     | :new_moon:     |
| setcos               | :new_moon:     | :new_moon:     | :new_moon:     |
| PIV-II               | :last_quarter_moon:     | :new_moon:     | :new_moon:     |
| cac                  | :full_moon:     | :new_moon:     | :new_moon:     |
| itacns               | :new_moon:     | :new_moon:     | :new_moon:     |
| isoApplet            | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| gids                 | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| openpgp              | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| jpki                 | :new_moon:     | :new_moon:     | :new_moon:     |
| npa                  | :new_moon:     | :new_moon:     | :new_moon:     |
| cac1                 | :full_moon:     | :new_moon:     | :new_moon:     |
| nqapplet             | :new_moon:     | :new_moon:     | :new_moon:     |
| skeid                | :new_moon:     | :new_moon:     | :new_moon:     |
| eOI                  | :new_moon:     | :new_moon:     | :new_moon:     |
| default              | :new_moon:     | :new_moon:     | :new_moon:     |

The table below shows a list of all tested smart cards that were used:

| Smart Card Driver | Tested Smart Cards                                               |
| ----------------- | ---------------------------------------------------------------- |
| PIV-II            | NIST Test Card v.1 9 (RSA2048), 4 (ECC), 13 (RSA1024, RSA2048)   |
|                   | NIST Test Card v.2 15 (ECC), 16 (RSA2048)                        |
|                   | Yubikey 4 OTP+U2F+CCID                                           |
| myeid             | Aventra MyEID v4.5 PKI card, Aventra MyEID v4.5.5, OsEID         |
| idprime           | IDPrime 940, IDPrime 930, IDPrime MD 830, IDPrime 3810           |
| cardos            | CardOS 5.3 (2023, 4k RSA, 2k RSA)                                |
| cac               | CAC test cards, virt_CACard                                      |
| cac1              | CAC test cards                                                   |
| openpgp           | OpenPGP Applet (JCardSim), Nitrokey Start (RTM.10), Nitrokey Pro 2 (0.14)|
| isoApplet         | IsoApplet v0 and v1 (JCardSim)                                   |
| epass2003         | ePass2003 PKI Token (2023)                                       |
| gids              | GIDS Applet  (JCardSim)                                          |
| coolkey           | Coolkey Applet (JCardSim)                                        |

### OpenSC 0.24.0

| Installer    | Windows    | macOS       |
| ------------ | ---------- | ----------- |
| Installation | :full_moon: | :new_moon:  |
| Removal      | :full_moon: | :new_moon:  |

The table below shows a list of all supported card drivers (`opensc-tool --list-drivers`) that have been tested in this release:

| Smart Card Driver    | PKCS#11        | Windows Minidriver | macOS CTK  |
| -------------------- | -------------- | -------------- | -------------- |
| cardos               | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| cyberflex            | :new_moon:     | :new_moon:     | :new_moon:     |
| gemsafeV1            | :new_moon:     | :new_moon:     | :new_moon:     |
| starcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| tcos                 | :new_moon:     | :new_moon:     | :new_moon:     |
| oberthur             | :new_moon:     | :new_moon:     | :new_moon:     |
| authentic            | :new_moon:     | :new_moon:     | :new_moon:     |
| iasecc               | :new_moon:     | :new_moon:     | :new_moon:     |
| belpic               | :new_moon:     | :new_moon:     | :new_moon:     |
| entersafe            | :new_moon:     | :new_moon:     | :new_moon:     |
| epass2003            | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| rutoken              | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken_ecp          | :new_moon:     | :new_moon:     | :new_moon:     |
| myeid                | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| dnie                 | :new_moon:     | :new_moon:     | :new_moon:     |
| MaskTech             | :new_moon:     | :new_moon:     | :new_moon:     |
| esteid2018           | :new_moon:     | :new_moon:     | :new_moon:     |
| idprime              | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| coolkey              | :full_moon:     | :new_moon:     | :new_moon:     |
| muscle               | :new_moon:     | :new_moon:     | :new_moon:     |
| sc-hsm               | :waning_gibbous_moon:     | :last_quarter_moon:    | :new_moon:     |
| mcrd                 | :new_moon:     | :new_moon:     | :new_moon:     |
| setcos               | :new_moon:     | :new_moon:     | :new_moon:     |
| PIV-II               | :last_quarter_moon:     | :last_quarter_moon:     | :new_moon:     |
| cac                  | :full_moon:     | :new_moon:     | :new_moon:     |
| itacns               | :new_moon:     | :new_moon:     | :new_moon:     |
| isoApplet            | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| gids                 | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| openpgp              | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| jpki                 | :new_moon:     | :new_moon:     | :new_moon:     |
| npa                  | :new_moon:     | :new_moon:     | :new_moon:     |
| cac1                 | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| nqapplet             | :new_moon:     | :new_moon:     | :new_moon:     |
| skeid                | :new_moon:     | :new_moon:     | :new_moon:     |
| default              | :new_moon:     | :new_moon:     | :new_moon:     |

The table below shows a list of all tested smart cards that were used:

| Smart Card Driver | Tested Smart Cards                                               |
| ----------------- | ---------------------------------------------------------------- |
| PIV-II            | PivApplet (JCardSim)                                             |
|                   | USB-C YubiKey 5C Nano                                            |
|                   | Thales IDPrime PIV v3.0 (detection only)                         |
|                   | NIST Test Card 9 (v.1)                                           |
| sc-hsm            | GoID 1.01,  SmartCard-HSM 4K USB-Token (2023)                    |
| cac               | CAC Test Cards (Oberthur ID One, Gemalto GCX4), virt_CACard      |
| coolkey           | 534e SafeNet Java Card with Coolkey Applet (1k RSA)              |
| gids              | GIDS Applet  (JCardSim)                                          |
| myeid             | OsEID, Aventra MyEID 4.5.5                                       |
| openpgp           | OpenPGP Applet  (JCardSim), NitroKey Start (gnuk)                |
| isoApplet         | IsoApplet v0 and v1 (JCardSim)                                   |
| cardos            | CardOS 5.3 (2017, 2k RSA), CardOS 5.3 (2023, 4k RSA)             |
| idprime           | IDPrime 940, IDPrime 930, IDPrime MD 830, IDPrime 3810           |
| epass2003         | ePass2003 PKI Token (2023)                                       |

### OpenSC 0.23.0

| Installer    | Windows    | macOS       |
| ------------ | ---------- | ----------- |
| Installation | :full_moon: | :new_moon:  |
| Removal      | :full_moon: | :new_moon:  |

The table below shows a list of all supported card drivers (`opensc-tool --list-drivers`) that have been tested in this release:

| Smart Card Driver    | PKCS#11        | Windows Minidriver | macOS Tokend   |
| -------------------- | -------------- | -------------- | -------------- |
| cardos               | :full_moon:    | :full_moon:    | :new_moon:     |
| cyberflex            | :new_moon:     | :new_moon:     | :new_moon:     |
| gemsafeV1            | :new_moon:     | :new_moon:     | :new_moon:     |
| starcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| tcos                 | :new_moon:     | :new_moon:     | :new_moon:     |
| oberthur             | :new_moon:     | :new_moon:     | :new_moon:     |
| authentic            | :new_moon:     | :new_moon:     | :new_moon:     |
| iasecc               | :new_moon:     | :new_moon:     | :new_moon:     |
| belpic               | :new_moon:     | :new_moon:     | :new_moon:     |
| entersafe            | :new_moon:     | :new_moon:     | :new_moon:     |
| epass2003            | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken              | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken_ecp          | :new_moon:     | :new_moon:     | :new_moon:     |
| myeid                | :new_moon:     | :new_moon:     | :new_moon:     |
| dnie                 | :new_moon:     | :new_moon:     | :new_moon:     |
| MaskTech             | :new_moon:     | :new_moon:     | :new_moon:     |
| esteid2018           | :new_moon:     | :new_moon:     | :new_moon:     |
| idprime              | :full_moon:    | :new_moon:     | :new_moon:     |
| coolkey              | :new_moon:     | :new_moon:     | :new_moon:     |
| muscle               | :new_moon:     | :new_moon:     | :new_moon:     |
| sc-hsm               | :full_moon:    | :full_moon:    | :new_moon:     |
| mcrd                 | :new_moon:     | :new_moon:     | :new_moon:     |
| setcos               | :new_moon:     | :new_moon:     | :new_moon:     |
| PIV-II               | :full_moon:    | :full_moon:    | :new_moon:     |
| cac                  | :full_moon:    | :new_moon:     | :new_moon:     |
| itacns               | :new_moon:     | :new_moon:     | :new_moon:     |
| isoApplet            | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| gids                 | :new_moon:     | :new_moon:     | :new_moon:     |
| openpgp              | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| jpki                 | :new_moon:     | :new_moon:     | :new_moon:     |
| npa                  | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| cac1                 | :full_moon:    | :new_moon:     | :new_moon:     |
| nqapplet             | :new_moon:     | :new_moon:     | :new_moon:     |
| default              | :new_moon:     | :new_moon:     | :new_moon:     |

The table below shows a list of all tested smart cards that were used:

| Smart Card Driver | Tested Smart Cards                                               |
| ----------------- | ---------------------------------------------------------------- |
| cardos            | CardOS 5.3, 2014                                                 |
|                   | CardOS 4.2+                                                      |
| cac               | 2 old CAC test cards                                             |
|                   | CAC HID Alt token                                                |
| cac1              | old CAC test card                                                |
| idprime           | IDPrime 3810                                                     |
| PIV-II            | IDEMIA Test card #1 ID-One PIV 2.4 on Cosmo v8.1                 |
|                   | NIST Test PIV card 6, v1                                         |
|                   | Yubikey 4 OTP+U2F+CCID                                           |
| sc-hsm            | GoID 1.01                                                        |

### OpenSC 0.22.0

| Installer    | Windows    | macOS       |
| ------------ | ---------- | ----------- |
| Installation | :full_moon:| :new_moon:  |
| Removal      | :full_moon:| :new_moon:  |

The table below shows a list of all supported card drivers (`opensc-tool --list-drivers`) that have been tested in this release:

| Smart Card Driver    | PKCS#11        | Windows Minidriver | macOS Tokend   |
| -------------------- | -------------- | -------------- | -------------- |
| cardos               | :full_moon:    | :new_moon:     | :new_moon:     |
| flex                 | :new_moon:     | :new_moon:     | :new_moon:     |
| cyberflex            | :new_moon:     | :new_moon:     | :new_moon:     |
| gpk                  | :new_moon:     | :new_moon:     | :new_moon:     |
| gemsafeV1            | :new_moon:     | :new_moon:     | :new_moon:     |
| asepcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| starcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| tcos                 | :new_moon:     | :new_moon:     | :new_moon:     |
| oberthur             | :new_moon:     | :new_moon:     | :new_moon:     |
| authentic            | :new_moon:     | :new_moon:     | :new_moon:     |
| iasecc               | :full_moon:    |:last_quarter_moon: | :new_moon: |
| belpic               | :new_moon:     | :new_moon:     | :new_moon:     |
| incrypto34           | :new_moon:     | :new_moon:     | :new_moon:     |
| akis                 | :new_moon:     | :new_moon:     | :new_moon:     |
| entersafe            | :new_moon:     | :new_moon:     | :new_moon:     |
| epass2003            | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken              | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken_ecp          | :new_moon:     | :new_moon:     | :new_moon:     |
| myeid                | :full_moon:    | :last_quarter_moon:     | :new_moon:     |
| dnie                 | :new_moon:     | :new_moon:     | :new_moon:     |
| MaskTech             | :new_moon:     | :new_moon:     | :new_moon:     |
| atrust-acos          | :new_moon:     | :new_moon:     | :new_moon:     |
| westcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| esteid2018           | :new_moon:     | :new_moon:     | :new_moon:     |
| idprime              | :full_moon:     | :new_moon:     | :new_moon:     |
| edo                  | :new_moon:     | :new_moon:     | :new_moon:     |
| coolkey              | :new_moon:     | :new_moon:     | :new_moon:     |
| muscle               | :new_moon:     | :new_moon:     | :new_moon:     |
| sc-hsm               | :last_quarter_moon:     | :new_moon:     | :new_moon:     |
| mcrd                 | :new_moon:     | :new_moon:     | :new_moon:     |
| setcos               | :new_moon:     | :new_moon:     | :new_moon:     |
| PIV-II               | :full_moon:     | :new_moon:     | :waning_crescent_moon:     |
| cac                  | :full_moon:     | :new_moon:     | :new_moon:     |
| itacns               | :new_moon:     | :new_moon:     | :new_moon:     |
| isoApplet            | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| gids                 | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| openpgp              | :full_moon:     | :new_moon:     | :new_moon:     |
| jpki                 | :new_moon:     | :new_moon:     | :new_moon:     |
| npa                  | :new_moon:     | :new_moon:     | :new_moon:     |
| cac1                 | :new_moon:     | :new_moon:     | :new_moon:     |
| default              | :new_moon:     | :new_moon:     | :new_moon:     |

The table below shows a list of all tested smart cards that were used:

| Smart Card Driver | Tested Smart Cards                                               |
| ----------------- | ---------------------------------------------------------------- |
| PIV-II            | PivApplet (JCardSim), Yubikey 4, NIST Test PIV cards (3-10, 12-14, 16)|
| cac               | virt_CACard (CI), HID CAC Alt token, old CAC cards               |
| coolkey           | 534e SafeNet Java Card with Coolkey Applet (CI)                  |
| gids              | GIDS Applet  1.3 (JCardSim)                                      |
| openpgp           | OpenPGP Applet  (JCardSim), Nitrokey Start                       |
| cardos            | CardOS 5.3                                                       |
| idprime           | IDPrime (OS v1)                                                  |
| sc-hsm            | GoID 1.01                                                        |
| isoApplet         | IsoApplet v0.6.1 (JCardSim)                                      |

### OpenSC 0.21.0

| Installer    | Windows    | macOS       |
| ------------ | ---------- | ----------- |
| Installation | :new_moon: | :full_moon: |
| Removal      | :new_moon: | :full_moon: |

The table below shows a list of all supported card drivers (`opensc-tool --list-drivers`) that have been tested in this release:

| Smart Card Driver    | PKCS#11        | Windows Minidriver | macOS Tokend   |
| -------------------- | -------------- | -------------- | -------------- |
| cardos               | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| flex                 | :new_moon:     | :new_moon:     | :new_moon:     |
| cyberflex            | :new_moon:     | :new_moon:     | :new_moon:     |
| gpk                  | :new_moon:     | :new_moon:     | :new_moon:     |
| gemsafeV1            | :new_moon:     | :new_moon:     | :new_moon:     |
| asepcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| starcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| tcos                 | :new_moon:     | :new_moon:     | :new_moon:     |
| oberthur             | :new_moon:     | :new_moon:     | :new_moon:     |
| authentic            | :new_moon:     | :new_moon:     | :new_moon:     |
| iasecc               | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| belpic               | :new_moon:     | :new_moon:     | :new_moon:     |
| incrypto34           | :new_moon:     | :new_moon:     | :new_moon:     |
| akis                 | :new_moon:     | :new_moon:     | :new_moon:     |
| entersafe            | :new_moon:     | :new_moon:     | :new_moon:     |
| epass2003            | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken              | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken_ecp          | :new_moon:     | :new_moon:     | :new_moon:     |
| myeid                | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| dnie                 | :new_moon:     | :new_moon:     | :new_moon:     |
| MaskTech             | :new_moon:     | :new_moon:     | :new_moon:     |
| atrust-acos          | :new_moon:     | :new_moon:     | :new_moon:     |
| westcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| esteid2018           | :new_moon:     | :new_moon:     | :new_moon:     |
| idprime              | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| edo                  | :new_moon:     | :new_moon:     | :new_moon:     |
| coolkey              | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| muscle               | :new_moon:     | :new_moon:     | :new_moon:     |
| sc-hsm               | :new_moon:     | :new_moon:     | :new_moon:     |
| mcrd                 | :new_moon:     | :new_moon:     | :new_moon:     |
| setcos               | :new_moon:     | :new_moon:     | :new_moon:     |
| PIV-II               | :full_moon:     | :last_quarter_moon:     | :full_moon:     |
| cac                  | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| itacns               | :new_moon:     | :new_moon:     | :new_moon:     |
| isoApplet            | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| gids                 | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| openpgp              | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| jpki                 | :new_moon:     | :new_moon:     | :new_moon:     |
| npa                  | :new_moon:     | :new_moon:     | :new_moon:     |
| cac1                 | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| default              | :new_moon:     | :new_moon:     | :new_moon:     |

The table below shows a list of all tested smart cards that were used:

| Smart Card Driver | Tested Smart Cards                                               |
| ----------------- | ---------------------------------------------------------------- |
| PIV-II            | PivApplet (JCardSim), YubiKey 5 Nano, NIST Test cards            |
| cac               | CAC Test Cards (Oberthur ID One, Gemalto GCX4), virt_CACard      |
| coolkey           | 534e SafeNet Java Card with Coolkey Applet                       |
| gids              | GIDS Applet  (JCardSim)                                          |
| iasecc            | Cosmo v8                                                         |
| myeid             | OsEID                                                            |
| openpgp           | OpenPGP Applet  (JCardSim)                                       |
| cardos            | CardOS 5.3 card                                                  |
| idprime           | idprime with os version 1                                        |

### OpenSC 0.20.0

| Installer    | Windows    | macOS      |
| ------------ | ---------- | ---------- |
| Installation | :new_moon: | :new_moon: |
| Removal      | :new_moon: | :new_moon: |

The table below shows a list of all supported card drivers (`opensc-tool --list-drivers`) that have been tested in this release:

| Smart Card Driver    | PKCS#11        | Windows Minidriver | macOS Tokend   |
| -------------------- | -------------- | -------------- | -------------- |
| cardos               | :new_moon:     | :new_moon:     | :new_moon:     |
| flex                 | :new_moon:     | :new_moon:     | :new_moon:     |
| cyberflex            | :new_moon:     | :new_moon:     | :new_moon:     |
| gpk                  | :new_moon:     | :new_moon:     | :new_moon:     |
| gemsafeV1            | :new_moon:     | :new_moon:     | :new_moon:     |
| miocos               | :new_moon:     | :new_moon:     | :new_moon:     |
| asepcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| starcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| tcos                 | :new_moon:     | :new_moon:     | :new_moon:     |
| jcop                 | :new_moon:     | :new_moon:     | :new_moon:     |
| oberthur             | :new_moon:     | :new_moon:     | :new_moon:     |
| authentic            | :new_moon:     | :new_moon:     | :new_moon:     |
| iasecc               | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| belpic               | :new_moon:     | :new_moon:     | :new_moon:     |
| incrypto34           | :new_moon:     | :new_moon:     | :new_moon:     |
| acos5                | :new_moon:     | :new_moon:     | :new_moon:     |
| akis                 | :new_moon:     | :new_moon:     | :new_moon:     |
| entersafe            | :new_moon:     | :new_moon:     | :new_moon:     |
| epass2003            | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken              | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken_ecp          | :new_moon:     | :new_moon:     | :new_moon:     |
| westcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| myeid                | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| sc-hsm               | :last_quarter_moon:     | :last_quarter_moon:     | :new_moon:     |
| dnie                 | :new_moon:     | :new_moon:     | :new_moon:     |
| MaskTech             | :new_moon:     | :new_moon:     | :new_moon:     |
| mcrd                 | :new_moon:     | :new_moon:     | :new_moon:     |
| setcos               | :new_moon:     | :new_moon:     | :new_moon:     |
| muscle               | :new_moon:     | :new_moon:     | :new_moon:     |
| atrust-acos          | :new_moon:     | :new_moon:     | :new_moon:     |
| PIV-II               | :last_quarter_moon:     | :last_quarter_moon:     | :new_moon:     |
| cac                  | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| itacns               | :new_moon:     | :new_moon:     | :new_moon:     |
| isoApplet            | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| gids                 | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| openpgp              | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| jpki                 | :new_moon:     | :new_moon:     | :new_moon:     |
| coolkey              | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| npa                  | :new_moon:     | :new_moon:     | :new_moon:     |
| default              | :new_moon:     | :new_moon:     | :new_moon:     |

The table below shows a list of all tested smart cards that were used:

| Smart Card Driver | Tested Smart Cards                                               |
| ----------------- | ---------------------------------------------------------------- |
| PIV-II            | PivApplet (JCardSim), YubiKey 5 Nano                             |
| cac               | CAC Test Cards (Oberthur ID One, Gemalto GCX4), virt_CACard      |
| coolkey           | 534e SafeNet Java Card with Coolkey Applet                       |
| gids              | GIDS Applet  (JCardSim)                                          |
| iasecc            | Gemalto MultiApp IAS/ECC v1.0.1                                  |
| myeid             | OsEID                                                            |
| openpgp           | OpenPGP Applet  (JCardSim)                                       |
| sc-hsm            | GoID 1.01                                                        |

### OpenSC 0.19.0

| Installer    | Windows    | macOS      |
| ------------ | ---------- | ---------- |
| Installation | :full_moon: | :full_moon: |
| Removal      | :full_moon: | :full_moon: |

The table below shows a list of all supported card drivers (`opensc-tool --list-drivers`) that have been tested in this release:

| Smart Card Driver    | PKCS#11        | Windows Minidriver | macOS Tokend   |
| -------------------- | -------------- | -------------- | -------------- |
| cardos               | :last_quarter_moon:     | :last_quarter_moon:     | :new_moon:     |
| flex                 | :new_moon:     | :new_moon:     | :new_moon:     |
| cyberflex            | :new_moon:     | :new_moon:     | :new_moon:     |
| gpk                  | :new_moon:     | :new_moon:     | :new_moon:     |
| gemsafeV1            | :new_moon:     | :new_moon:     | :new_moon:     |
| miocos               | :new_moon:     | :new_moon:     | :new_moon:     |
| asepcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| starcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| tcos                 | :new_moon:     | :new_moon:     | :new_moon:     |
| jcop                 | :new_moon:     | :new_moon:     | :new_moon:     |
| oberthur             | :new_moon:     | :new_moon:     | :new_moon:     |
| authentic            | :new_moon:     | :new_moon:     | :new_moon:     |
| iasecc               | :last_quarter_moon:     | :new_moon:     | :new_moon:     |
| belpic               | :new_moon:     | :new_moon:     | :new_moon:     |
| incrypto34           | :new_moon:     | :new_moon:     | :new_moon:     |
| acos5                | :new_moon:     | :new_moon:     | :new_moon:     |
| akis                 | :new_moon:     | :new_moon:     | :new_moon:     |
| entersafe            | :new_moon:     | :new_moon:     | :new_moon:     |
| epass2003            | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken              | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken_ecp          | :new_moon:     | :new_moon:     | :new_moon:     |
| westcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| myeid                | :full_moon:    | :last_quarter_moon: | :new_moon:     |
| sc-hsm               | :waning_gibbous_moon:     | :waning_gibbous_moon:     | :waning_gibbous_moon:     |
| dnie                 | :new_moon:     | :new_moon:     | :new_moon:     |
| MaskTech             | :new_moon:     | :new_moon:     | :new_moon:     |
| mcrd                 | :new_moon:     | :new_moon:     | :new_moon:     |
| setcos               | :new_moon:     | :new_moon:     | :new_moon:     |
| muscle               | :new_moon:     | :new_moon:     | :new_moon:     |
| atrust-acos          | :new_moon:     | :new_moon:     | :new_moon:     |
| PIV-II               | :full_moon:    | :new_moon:     | :new_moon:     |
| cac                  | :full_moon:    | :new_moon:     | :new_moon:     |
| itacns               | :new_moon:     | :new_moon:     | :new_moon:     |
| isoApplet            | :new_moon:     | :new_moon:     | :new_moon:     |
| gids                 | :new_moon:     | :new_moon:     | :new_moon:     |
| openpgp              | :new_moon:     | :new_moon:     | :new_moon:     |
| jpki                 | :new_moon:     | :new_moon:     | :new_moon:     |
| coolkey              | :full_moon:    | :new_moon:     | :new_moon:     |
| npa                  | :new_moon:     | :new_moon:     | :new_moon:     |
| default              | :new_moon:     | :new_moon:     | :new_moon:     |

The table below shows a list of all tested smart cards that were used:

| Smart Card Driver | Tested Smart Cards                                               |
| ----------------- | ---------------------------------------------------------------- |
| cardos            | CardOS 4.3B                                                      |
| cardos            | Atos CardOS 5.3 cards with RSA                                   |
| sc-hsm            | GoID 1.00                                                        |
| iasecc            | Gemalto MultiApp IAS/ECC v1.0.1                                  |
| myeid             | MyEID cards with PKCS#15-applet                                  |
| PIV-II            | NIST Test PIV Cards 8 and 4                                      |
| cac               | CAC Test Cards (Oberthur ID One, Gemalto GCX4)                   |
| coolkey           | 534e SafeNet Java Card with Coolkey Applet                       |

### OpenSC 0.18.0

| Installer    | Windows    | macOS      |
| ------------ | ---------- | ---------- |
| Installation | :full_moon: | :full_moon: |
| Removal      | :full_moon: | :full_moon: |

The table below shows a list of all supported card drivers (`opensc-tool --list-drivers`) that have been tested in this release:

| Smart Card Driver    | PKCS#11    | Windows Minidriver | macOS Tokend   |
| -------------------- | -------------- | -------------- | -------------- |
| cardos               | :waning_gibbous_moon: | :last_quarter_moon: | :new_moon:     |
| flex                 | :new_moon:     | :new_moon:     | :new_moon:     |
| cyberflex            | :new_moon:     | :new_moon:     | :new_moon:     |
| gpk                  | :new_moon:     | :new_moon:     | :new_moon:     |
| gemsafeV1            | :new_moon:     | :new_moon:     | :new_moon:     |
| miocos               | :new_moon:     | :new_moon:     | :new_moon:     |
| asepcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| starcos              | :waning_crescent_moon: | :waning_crescent_moon:     | :new_moon:     |
| tcos                 | :new_moon:     | :new_moon:     | :new_moon:     |
| jcop                 | :new_moon:     | :new_moon:     | :new_moon:     |
| oberthur             | :new_moon:     | :new_moon:     | :new_moon:     |
| authentic            | :new_moon:     | :new_moon:     | :new_moon:     |
| iasecc               | :waning_crescent_moon: | :new_moon:     | :new_moon:     |
| belpic               | :new_moon:     | :new_moon:     | :new_moon:     |
| incrypto34           | :new_moon:     | :new_moon:     | :new_moon:     |
| acos5                | :new_moon:     | :new_moon:     | :new_moon:     |
| akis                 | :new_moon:     | :new_moon:     | :new_moon:     |
| entersafe            | :new_moon:     | :new_moon:     | :new_moon:     |
| epass2003            | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken              | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken_ecp          | :new_moon:     | :new_moon:     | :new_moon:     |
| westcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| myeid                | :full_moon:     | :new_moon:     | :new_moon:     |
| sc-hsm               | :waning_gibbous_moon: | :waning_gibbous_moon: | :new_moon:     |
| dnie                 | :new_moon:     | :new_moon:     | :new_moon:     |
| MaskTech             | :new_moon:     | :new_moon:     | :new_moon:     |
| mcrd                 | :new_moon:     | :new_moon:     | :new_moon:     |
| setcos               | :new_moon:     | :new_moon:     | :new_moon:     |
| muscle               | :new_moon:     | :new_moon:     | :new_moon:     |
| atrust-acos          | :new_moon:     | :new_moon:     | :new_moon:     |
| PIV-II               | :waning_gibbous_moon:     | :new_moon:     | :new_moon:     |
| cac                  | :new_moon:     | :new_moon:     | :new_moon:     |
| itacns               | :new_moon:     | :new_moon:     | :new_moon:     |
| isoApplet            | :new_moon:     | :new_moon:     | :new_moon:     |
| gids                 | :new_moon:     | :new_moon:     | :new_moon:     |
| openpgp              | :waning_crescent_moon: | :new_moon:     | :new_moon:     |
| jpki                 | :new_moon:     | :new_moon:     | :new_moon:     |
| coolkey              | :new_moon:     | :new_moon:     | :new_moon:     |
| npa                  | :waning_crescent_moon:     | :new_moon:     | :new_moon:     |
| default              | :new_moon:     | :new_moon:     | :new_moon:     |

The table below shows a list of all tested smart cards that were used:

| Smart Card Driver | Tested Smart Cards                                               |
| ----------------- | ---------------------------------------------------------------- |
| cardos            | CardOS 4.3B                                                      |
| sc-hsm            | GoID 1.00                                                        |
| starcos           | Starcos 3.4                                                      |
| openpgp           | OpenPGP Card 3.0                                                 |
| iasecc            | Gemalto MultiApp IAS/ECC v1.0.1                                  |
| myeid             | MyEID cards with PKCS#15-applet                                  |
| PIV-II            | NIST DEMO cards 1 and 7(with history object: 3 and 2)            |

### OpenSC 0.17.0

| Installer    | Windows            | macOS              |
| ------------ | :----------------: | :----------------: |
| Installation | :full_moon: | :full_moon: |
| Removal      | :full_moon: | :full_moon: |

The table below shows a list of all supported card drivers (`opensc-tool --list-drivers`) that have been tested in this release:

| Smart Card Driver    | PKCS#11            | Windows Minidriver | macOS Tokend       |
| -------------------- | :----------------: | :----------------: | :----------------: |
| cardos               | :full_moon: | :full_moon: | :full_moon: |
| flex                 | :new_moon:     | :new_moon:     | :new_moon:     |
| cyberflex            | :new_moon:     | :new_moon:     | :new_moon:     |
| gpk                  | :new_moon:     | :new_moon:     | :new_moon:     |
| gemsafeV1            | :new_moon:     | :new_moon:     | :new_moon:     |
| miocos               | :new_moon:     | :new_moon:     | :new_moon:     |
| asepcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| starcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| tcos                 | :new_moon:     | :new_moon:     | :new_moon:     |
| jcop                 | :new_moon:     | :new_moon:     | :new_moon:     |
| oberthur             | :new_moon:     | :new_moon:     | :new_moon:     |
| authentic            | :new_moon:     | :new_moon:     | :new_moon:     |
| iasecc               | :last_quarter_moon:           | :new_moon:     | :last_quarter_moon:           |
| belpic               | :full_moon: | :new_moon:     | :new_moon:     |
| incrypto34           | :new_moon:     | :new_moon:     | :new_moon:     |
| acos5                | :new_moon:     | :new_moon:     | :new_moon:     |
| akis                 | :new_moon:     | :new_moon:     | :new_moon:     |
| entersafe            | :full_moon: | :full_moon: | :full_moon: |
| epass2003            | :full_moon: | :full_moon: | :full_moon: |
| rutoken              | :new_moon:     | :new_moon:     | :new_moon:     |
| rutoken_ecp          | :new_moon:     | :new_moon:     | :new_moon:     |
| westcos              | :new_moon:     | :new_moon:     | :new_moon:     |
| myeid                | :last_quarter_moon:           | :new_moon:     | :new_moon:     |
| sc-hsm               | :full_moon: | :full_moon: | :full_moon: |
| dnie                 | :full_moon: | :new_moon:     | :new_moon:     |
| MaskTech             | :new_moon:     | :new_moon:     | :new_moon:     |
| mcrd                 | :new_moon:     | :new_moon:     | :new_moon:     |
| setcos               | :new_moon:     | :new_moon:     | :new_moon:     |
| muscle               | :new_moon:     | :new_moon:     | :new_moon:     |
| atrust-acos          | :new_moon:     | :new_moon:     | :new_moon:     |
| PIV-II               | :full_moon: | :new_moon:     | :new_moon:     |
| cac                  | :full_moon: | :new_moon:     | :new_moon:     |
| itacns               | :new_moon:     | :new_moon:     | :new_moon:     |
| isoApplet (no ECDSA) | :last_quarter_moon:           | :new_moon:     | :new_moon:     |
| gids                 | :new_moon:     | :new_moon:     | :new_moon:     |
| openpgp              | :last_quarter_moon:           | :new_moon:     | :new_moon:     |
| jpki                 | :full_moon: | :last_quarter_moon:           | :last_quarter_moon:           |
| coolkey              | :full_moon: | :new_moon:     | :new_moon:     |
| npa                  | :last_quarter_moon:           | :new_moon:     | :last_quarter_moon:           |
| default              | :new_moon:     | :new_moon:     | :new_moon:     |

The table below shows a list of all tested smart cards that were used:

| Smart Card Driver | Tested Smart Cards                                               |
| ----------------- | ---------------------------------------------------------------- |
| cardos            | CardOS 4.3B                                                      |
| sc-hsm            | GoID 0.9                                                         |
| npa               | German ID card                                                   |
| PIV-II            | NIST demo PIV card 1, 10                                         |
| cac               | Expired test CAC card                                            |
| iasecc            | IAS/ECC Gemalto, Gemalto MultiApp IAS/ECC v1.0.1                 |
| openpgp           | OpenPGP v2.0 card (ZeitControl)                                  |
| openpgp           | Yubikey NEO                                                      |
| coolkey           | coolkey applet on SafeNet Java card                              |
| sc-hsm            | Nitrokey Nitrokey HSM                                            |
| cardos            | CardOS 5.3                                                       |
| isoApplet         | IsoApplet in Swissbit secure microSD card                        |

## Test Cases

### Installer

#### Installation

##### Windows

**Test Steps:**

1. Open the OpenSC installer (*msi* file extension)
2. Follow the prompts for installation.

**Expected Result:**
OpenSC has been installed

##### macOS

**Test Steps:**

1. Open the OpenSC image (*dmg* file extension)
2. Open the OpenSC installer (*pkg* file extension)
3. Follow the prompts for installation.

**Expected Result:**
OpenSC has been installed

#### Removal

##### Windows

**Test Steps:**

1. Open Control Panel
2. In Category view, click the "Uninstall a program" link under the "Programs" category. In Icon view, click the "Programs and Features" icon.
3. Find and select "OpenSC smartcard framework".
4. Click "Uninstall"
5. Depending on which programs have loaded OpenSC, you will be prompted to reboot.

**Expected Result:**
OpenSC is removed.

##### macOS

**Test Steps:**

1. Open a command line terminal (*Terminal.app*)
2. Run `sudo opensc-uninstall`
3. Enter your password

**Expected Result:**
OpenSC is removed.

### PKCS#11

#### `pkcs11-tool`

Test random number generation, digest calculation, signature, verification and decryption with the token using the PKCS#11 API.

```bash
pkcs11-tool --test -p "$PIN" --module "/path/to/opensc-pkcs11.so"
```

#### `pkcs11`

Test signature, verification, decryption, key wrapping and derivation in various modes with the token using the PKCS#11 API.

```bash
p11test -p "$PIN" -m "/path/to/opensc-pkcs11.so"
```

If not sure, whether the output signifies correct results, compare results with older OpenSC version.

##### Windows

**Test Steps:**

1. Open a command line terminal (*cmd.exe*).
2. Run `"C:\Program Files\OpenSC Project\OpenSC\tools\pkcs11-tool.exe" --login --test`

**Expected Result:**
`No errors`

##### Other Operating Systems

**Test Steps:**

1. Open a command line terminal.
2. Run `pkcs11-tool --login --test`

**Expected Result:**
`No errors`

#### Firefox

##### Load OpenSC PKCS#11 Module

**Test Steps:**

1. Open the Firefox preferences dialog. Choose *Advanced* > *Encryption* > *Security Devices*
2. Choose *Load*
3. Enter a name for the security module, such as "OpenSC".
4. Choose "Browse..." to find the location of the PKCS11 module on your local computer, and choose "OK" when done.

|          | Location of PKCS#11 module                          |
| -------- | --------------------------------------------------- |
| Windows  | `C:\Windows\System32\onepsc-pkcs11.dll`      |
| macOS    | `/Library/OpenSC/lib/opensc-pkcs11.so`       |
| Other OS | `/usr/local/lib/opensc-pkcs11.so` by default |

**Expected Result:**
Certificates and private keys are verified (see command line output). The token's certificates are shown in a dialog.

##### PIN Verification

**Preconditions:**

* OpenSC PKCS#11 module is loaded

**Test Steps:**

1. Put the token on the reader.
2. Open the Firefox preferences dialog. Choose *Advanced* > *Encryption* > *Security Devices*
3. Select your Token from the *OpenSC* security device
4. Click *Log In* and verify your PIN

**Expected Result:**
User is logged in. The *Log Out* button becomes available.

#### TLS Client Authentication

**Preconditions:**

* The web server is configured for client authentication with the token's certificate (for example [./gnutls-http-serv](https://gitlab.com/gnutls/gnutls/blob/master/doc/credentials/gnutls-http-serv) with `--x509cafile` with issuers certificate or certificate from the card).

**Test Steps:**

1. Put the token on the reader.
2. Browse to the web server.
3. Select a certificate from the token for authentication in the popup dialog.
4. Verify your PIN.

**Expected Result:**
User is authenticated.

#### OpenSSH

**Preconditions:**

* SSH server with allowed public key authentication (for example `localhost`)
* Inserted card with either generally-visible RSA or ECDSA public keys or X.509 certificates

##### Without ssh-agent

**Test steps:**

1. Get the public keys from the card in OpenSSH format (for path the PKCS#11 library in your system use above table)

    ```sh
    ssh-keygen -D /usr/local/lib/opensc-pkcs11.so
    ```

2. Store the key(s) in `~/.ssh/authorized_keys` in server
3. Try to connect with ssh to this server:

    ```sh
    ssh -I /usr/local/lib/opensc-pkcs11.so example.com
    ```

**Expected Result:**

* You are prompted for a PIN
* You are authenticated to the server (and usually given a remote shell)

##### With ssh-agent

**Test steps:**

1. Start `ssh-agent` in current terminal window (if is not already running):

    ```sh
    eval $(ssh-agent)
    ```

2. Add the smartcard to the `ssh-agent` (for path the PKCS#11 library in your system use above table) and write your PIN:

    ```sh
    ssh-add -s /usr/local/lib/opensc-pkcs11.so
    ```

3. Get the public keys from the card in OpenSSH format:

    ```sh
    ssh-add -L
    ```

4. Store the key(s) in `~/.ssh/authorized_keys` in server
5. Try to connect (repetitively) with ssh to this server:

    ```sh
    ssh example.com
    ```

**Expected Result:**

* You are NOT prompted for a PIN during each connection
* You are authenticated to the server (and usually given a remote shell)

Note: This will not work for keys with `ALWAYS_AUTHENTICATE` attribute, because of [OpenSSH bug #2638](https://bugzilla.mindrot.org/show_bug.cgi?id=2638)

### Windows Minidriver

#### `certutil -scinfo`

Verify certificates and test private keys of the token.

**Test Steps:**

1. Open a command line terminal (*cmd.exe*).
2. Run `certutil -scinfo`

**Expected Result:**
Certificates and private keys are verified (see command line output). The token's certificates are shown in a dialog.

#### Windows Login or Unlock

**Preconditions:**

* The user's account is configured for login with the token's certificate.
* Screen is locked by the user or the user is logged out.

**Test Steps:**

1. Put the token on the reader.
2. If needed, choose *Other Credentials* and select the smart card.
3. Verify your PIN.

**Expected Result:**
User is logged in.

#### TLS Client Authentication with Internet Explorer, Edge or Chrome

**Preconditions:**

* The web server is configured for client authentication with the token's certificate.

**Test Steps:**

1. Put the token on the reader.
2. Browse to the web server.
3. Select a certificate from the token for authentication in the popup dialog.
4. Verify your PIN.

**Expected Result:**
User is authenticated.

#### Change PIN

**Test Steps:**

1. Put the token on the reader.
2. Press *CTRL* + *Alt* + *Del*
3. Choose *Change a password*
4. Choose *Other Credentials* and select the smart card.
5. Without a PIN pad reader change your PIN with the screen shown. With a PIN pad reader, leave the text fields empty and change your PIN on the reader.

**Expected Result:**
PIN is changed.

### macOS Tokend

#### Keychain Access

**Test Steps:**

1. Put the token on the reader.
2. Open Keychain Access (*keychainaccess.app*), which is in the Utilities folder of your Applications folder.
3. Find and click your token in the *Keychains* panel in the upper left. The main window shows the token's certificate.
4. Click the closed lock in the upper left corner to verify your PIN.

**Expected Result:**
User is logged in, the lock is unlocked.

#### TLS Client Authentication with Safari or Chrome

**Preconditions:**

* The web server is configured for client authentication with the token's certificate.

**Test Steps:**

1. Put the token on the reader.
2. Browse to the web server.
3. Select a certificate from the token for authentication in the popup dialog.
4. Verify your PIN.

**Expected Result:**
User is authenticated.

### Detaching and attaching reader with card inserted

**Preconditions:**

* A ThinLinc client using smart card authentication.

1. Download ThinLinc client here <https://www.cendio.com/thinlinc/download> and install it.
2. The ThinLinc client must be configured to run with the new OpenSC release. Run this in your home directory (Change `<PATH TO PKCS11 MODULE>` to the absolute path to  `opensc-pkcs11.so` of the new release):

    ```sh
    mkdir -p .thinlinc && echo "PKCS11_MODULE=<PATH TO PKCS11 MODULE>" >> .thinlinc/tlclient.conf
    ```

3. Enable smart card authentication in the ThinLinc client:

    ```sh
    echo "AUTHENTICATION_METHOD=scpublickey" >> .thinlinc/tlclient.conf
    ```

4. Start ThinLinc client.
5. Put the card in the reader and the certificate should be shown in the "Certificate" scroll down menu.

**Test Steps:**

* Detach the entire reader with card still in it.
* Attach the reader again with the card in it.

**Expected Result:**

* The certificate is removed from the "Certificate" scroll down list when the reader is detached.
* The certificates is shown again when the reader is attached.
