# Home

OpenSC provides a set of libraries and utilities to work with smart cards. Its main focus is on cards that support cryptographic operations, and facilitate their use in security applications such as authentication, mail encryption and digital signatures. OpenSC implements the standard APIs to smart cards, e.g. [PKCS#11 API](https://www.oasis-open.org/committees/pkcs11/), [Windows' Smart Card Minidriver](https://learn.microsoft.com/en-us/windows-hardware/drivers/smartcard/smart-card-minidriver-overview) and macOS CryptoTokenKit.

## Download

You can find the updated download links in the README or on the main github page.
Do NOT attempt to add them here or use any links from wiki to download software as wiki is open to anyone to edit and the links were spoofed previously. For more information, see [issue #2554](https://github.com/OpenSC/OpenSC/issues/2554).

## News

* 14.01.2025: [OpenSC 0.26.1](https://github.com/OpenSC/OpenSC/releases/tag/0.26.1) is available.
* 13.11.2024: [OpenSC 0.26.0](https://github.com/OpenSC/OpenSC/releases/tag/0.26.0) is available.
* 05.04.2024: [OpenSC 0.25.1](https://github.com/OpenSC/OpenSC/releases/tag/0.25.1) is available.
* 06.03.2024: [OpenSC 0.25.0](https://github.com/OpenSC/OpenSC/releases/tag/0.25.0) is available.
* 13.12.2023: [OpenSC 0.24.0](https://github.com/OpenSC/OpenSC/releases/tag/0.24.0) is available.
* 29.11.2022: [OpenSC 0.23.0](https://github.com/OpenSC/OpenSC/releases/tag/0.23.0) is available.
* 20.10.2021: **SECURITY**: Data handling issues prior to 0.22.0 (CVE-2021-42778, CVE-2021-42779, CVE-2021-42780, CVE-2021-42781, CVE-2021-42782), see [security advisories](OpenSC-security-advisories).
* 10.08.2021: [OpenSC 0.22.0](https://github.com/OpenSC/OpenSC/releases/tag/0.22.0) is available.
* 24.11.2020: **SECURITY**: Data handling issues prior to 0.21.0 (CVE-2020-26570, CVE-2020-26571, CVE-2020-26572), see [security advisories](OpenSC-security-advisories).
* 24.11.2020: [OpenSC 0.21.0](https://github.com/OpenSC/OpenSC/releases/tag/0.21.0) is available.
* 29.12.2019: **SECURITY**: Data handling issues prior to 0.20.0 (CVE-2019-6502, CVE-2019-15946, CVE-2019-15945, CVE-2019-19480, CVE-2019-19481, CVE-2019-19479), see [security advisories](OpenSC-security-advisories).
* 29.12.2019: [OpenSC 0.20.0](https://github.com/OpenSC/OpenSC/releases/tag/0.20.0) is available.
* 13.09.2018: **SECURITY**: Response APDU handling issues prior to 0.19.0 (CVE-2018-16391, CVE-2018-16392, CVE-2018-16393, CVE-2018-16418, CVE-2018-16419, CVE-2018-16420, CVE-2018-16421, CVE-2018-16422, CVE-2018-16423, CVE-2018-16424, CVE-2018-16425, CVE-2018-16426, CVE-2018-16427), see [security advisories](OpenSC-security-advisories).
* 13.09.2018: [OpenSC 0.19.0](https://github.com/OpenSC/OpenSC/releases/tag/0.19.0) is available.
* 16.05.2018: [OpenSC 0.18.0](https://github.com/OpenSC/OpenSC/releases/tag/0.18.0) is available.
* 03.02.2018: [Smart Cards in Linux and why you should care](https://archive.fosdem.org/2018/schedule/event/smartcards_in_linux/attachments/slides/2265/export/events/attachments/smartcards_in_linux/slides/2265/smart_cards_slides.pdf): Talk at [FOSDEM 2018](https://archive.fosdem.org/2018/schedule/speaker/jakub_jelen/)
* 18.07.2017: [OpenSC 0.17.0](https://github.com/OpenSC/OpenSC/releases/tag/0.17.0) is available.
* 03.06.2016: [OpenSC 0.16.0](https://github.com/OpenSC/OpenSC/releases/tag/0.16.0) is available.
* 03.09.2015: [Continuous integration for Windows](https://ci.appveyor.com/project/LudovicRousseau/OpenSC/branch/master) with AppVeyor
* 16.05.2015: [OpenSC 0.15.0](https://sourceforge.net/projects/opensc/files/OpenSC/opensc-0.15.0/) is available.
* 01.04.2015: Continuous integration for macOS with Travis CI
* 24.01.2015: [Static code analysis](https://scan.coverity.com/projects/opensc-opensc) with Coverity Scan
* 21.01.2015: [Continuous integration for Linux](https://travis-ci.org/OpenSC/OpenSC/branches) with Travis CI
* 30.06.2014: [OpenSC 0.14.0](https://sourceforge.net/projects/opensc/files/OpenSC/opensc-0.14.0/) is available.
* 04.12.2012: [OpenSC 0.13.0](https://sourceforge.net/projects/opensc/files/OpenSC/opensc-0.13.0/) is available.
* 05.02.2011: OpenSC was at [FOSDEM 2012](OpenSC-@-FOSDEM-2012).
* 14.07.2011: [OpenSC 0.12.2](https://sourceforge.net/projects/opensc/files/OpenSC/opensc-0.12.2/) is available.
* 09.06.2011: [eID interoperability through open source software](http://www.slideshare.net/martinpaljak/opensc-eid-interoperability-through-open-source-software-8255721): Talk at eID Management Conference
* 18.04.2011: OpenSC 0.12.1 is available.
* 14.04.2011: Nightly builds are available for Windows and Mac OS X users.
* 05.02.2011: OpenSC was at [FOSDEM 2011](OpenSC-@-FOSDEM-2011).
* 17.12.2010: **SECURITY**: Response APDU handling issues prior to 0.12.0 (CVE-2010-4523), see [security advisories](OpenSC-security-advisories).

See [History of the OpenSC Project](History-of-the-OpenSC-Project) for older history.

## Sub-projects

OpenSC effort consists of various sub-projects that can be used independently as well, without OpenSC:

* [libp11](https://github.com/OpenSC/libp11) is a wrapper library for PKCS#11 modules with OpenSSL interface,
* [pkcs11-helper](https://github.com/OpenSC/pkcs11-helper) is a wrapper library for PKCS#11 modules with extended callback mechanisms for user and token interaction,
* [PAM-PKCS#11](https://github.com/OpenSC/pam_pkcs11/wiki) is a feature rich pluggable authentication module (PAM) for authentication via PKCS#11 modules, which includes various tools to controls the login process,
* [pam_p11](https://github.com/OpenSC/pam_p11) is a simple pluggable authentication module (PAM) for authentication via PKCS#11 modules,
* [OpenCT](https://github.com/OpenSC/openct/wiki) implements a reader driver interface for various non-standard readers on Linux,
* [OpenSC-Java](https://github.com/CardContact/opensc-java) is a Java PKCS#11 wrapper and JCE Provider,
* [OpenSC.tokend](https://github.com/OpenSC/OpenSC.tokend) is the open source tokend implementation from OpenSC.

## License

OpenSC is written by an international team of volunteers and is licensed as [Open Source](http://www.opensource.org/) software under the  [LGPL license](http://www.opensource.org/licenses/lgpl-license.php) version 2.1 of the License, or (at your option) any later version. For a list of all authors and contributors as well as detailed license information see [OpenSC-Credits](OpenSC-Credits).
