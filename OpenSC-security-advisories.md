# OpenSC security advisories

Software often contains bugs, so does OpenSC. Be aware of the following security issues (in addition to overall [security considerations](Security-Considerations)) and upgrade to latest released version if needed.

* 30.03.2026 Several data handling issues affecting releases before 0.27.0
  * [CVE-2025-49010](CVE-2025-49010): Possible write beyond buffer bounds during processing of GET RESPONSE APDU
  * [CVE-2025-66037](CVE-2025-66037): Possible buffer overrun while parsing SPKI
  * [CVE-2025-66038](CVE-2025-66038): Possible read beyond buffer bounds when parsing historical bytes in PIV driver
  * [CVE-2025-13763](CVE-2025-13763): Various uninitialized memory usage during card detection in libopensc
  * [CVE-2025-66215](CVE-2025-66215): Possible write beyond buffer bounds in oberthur driver
  * More low-severity data handling issues when parsing profile configuration
* 05.09.2024 Heap buffer overflow issue has been identified in OpenSC driver for OpenPGP [CVE-2024-8443](CVE-2024-8443)
* 04.09.2024 Uninitialized memory issues have been identified in OpenSC
  * [CVE-2024-45615](CVE-2024-45615): Usage of uninitialized values in libopensc and pkcs15init
  * [CVE-2024-45616](CVE-2024-45616): Uninitialized values after incorrect check or usage of APDU response values in libopensc
  * [CVE-2024-45617](CVE-2024-45617): Uninitialized values after incorrect or missing checking return values of functions in libopensc
  * [CVE-2024-45618](CVE-2024-45618): Uninitialized values after incorrect or missing checking return values of functions in pkcs15init
  * [CVE-2024-45619](CVE-2024-45619): Incorrect handling length of buffers or files in libopensc
  * [CVE-2024-45620](CVE-2024-45620): Incorrect handling of the length of buffers or files in pkcs15init*
* 13.02.2024 Heap use after free issue and vulnerability to Marvin attack have been identified in OpenSC
  * Side-channel leaks while stripping encryption PKCS#1.5 padding [CVE-2023-5992|](CVE-2023-5992)
  * Memory use after free in AuthentIC driver when updating token info [CVE-2024-1454](CVE-2024-1454)
* 25.09.2023 Multiple issues have been identified in OpenSC, mostly buffer overflows, but also potential PIN bypass.
  * The memory issues can be triggered by malicious smartcards sending malformed responses to APDU commands. Coded as ([CVE-2023-40661](CVE-2023-40661) and [CVE-2023-4535](CVE-2023-4535)).
  * The potential PIN bypass can happen when card tracks its own login state, demonstrated with Yubikey's PIV applet [CVE-2023-40660](CVE-2023-40660)
* 20.10.2021 Multiple issues have been identified in OpenSC, including heap double free, use after free/return, and buffer overflows. They can be triggered by malicious smartcards sending malformed responses to APDU commands. Coded as ([CVE-2021-42778](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2021-4277), [CVE-2021-42779](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2021-42779), [CVE-2021-42780](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2021-42780) and [CVE-2021-42781](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2021-42782))
* 24.11.2020 Heap buffer overflows have been detected in the smart card drivers for oberthur, TCOS and Gemsafe GPK, which can be triggered by a specially crafted smart card during the initialization of OpenSC ([CVE-2020-26570](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2020-26570), [CVE-2020-26571](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2020-26571) and [CVE-2020-26572](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2020-26572))
* [13.09.2018](https://sourceforge.net/p/opensc/mailman/message/36414448/) Multiple issues have been identified in OpenSC, ranging from stack based buffer overflows to out of bounds reads and writes on the heap. They can be triggered by malicious smartcards sending malformed responses to APDU commands. Source: [X41-2018-002](https://www.x41-dsec.de/lab/advisories/x41-2018-002-OpenSC/). Coded as [CVE-2018-16391](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2018-16391), [CVE-2018-16392](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2018-16392), [CVE-2018-16393](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2018-16393), [CVE-2018-16418](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2018-16418), [CVE-2018-16419](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2018-16419), [CVE-2018-16420](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2018-16420), [CVE-2018-16421](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2018-16421), [CVE-2018-16422](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2018-16422), [CVE-2018-16423g](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2018-16423), [CVE-2018-16424](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2018-16424), [CVE-2018-16425](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2018-16425), [CVE-2018-16426](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2018-16426) and [CVE-2018-16427](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2018-16427)
* 17.12.2010 A rogue smart card, specially crafted for this purpose, can be used to potentially execute arbitrary code if inserted to a local machine. Source: MWR InfoSecurity Advisory. Coded as [CVE-2010-4523](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2010-4523)
* 07.05.2009 security advisory coded as [CVE-2009-1603](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2009-1603)
* 26.02.2009 security advisory coded as [CVE-2009-0368](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2009-0368)
* 27.08.2008 security advisory coded as [CVE-2008-3972](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2008-3972)
* 31.07.2008 security advisory coded as [CVE-2008-2235](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2008-2235)

If you have other [security related issues](https://github.com/OpenSC/OpenSC/security/advisories/new) to report, they can be filed without public visibility.
