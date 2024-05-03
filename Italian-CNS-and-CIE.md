# Italian CNS and CIE

CNS stands for Carta Nazionale dei Servizi (National Service Card); CIE stands for Carta d'Identità Elettronica (Electronic Identity Card). From the viewpoint of the software there is not much difference between them: the basic filesystem layout is very similar and the Functional Specifications detailing the APDU commands are almost identical. The two cards exist because:

* The CIE can be used as a physical ID card, but not the CNS;
* A single citizen can own any number of CNS cards, but at most one CIE card (in place of the "paper" version).
* The CNS is issued by Public Administrations, leveraging on services provided by a qualified Certification Authority.
* The CIE is issued by the italian Ministry of Interior, Municipalities act as Registration Autorities.
* CNS cannot be issued to a citizen who already owns a CIE.

The filesystem layout is flexible. A lot of different administrations issue CNS cards; each administration personalizes the card with its own "service installation" public key. Authentication with the matching private key provides the ability to add support for custom additional objects after the card has been issued. Some Regions have prepared their cards to store medical data in accordance to the NETLINK standard; Chambers of Commerce issue CNS cards with additional signature keys. Third parties can register with the CNIPA government agency and obtain the "allocation of file IDs" for their applications; then the CNS issuer may install the files.

All CNS/CIE cards carry one X.509 certificate with its public and private keys, mostly used for on-line authentication via SSL. Encryption, decryption, signature with this certificate is the basic functionality currently supported by the `itacns` driver.

## References

* [CNS specs](https://www.agid.gov.it/sites/default/files/repository_files/documentazione_trasparenza/cns_functional_specification_1.1.6_02042011.pdf)
* [CIE specs](https://developers.italia.it/en/cie/)
