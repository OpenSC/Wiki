# US CAC

The US Army is using the Common Access Cards (CAC), specified by the [Global Smart Card Interoperability Specification (GSC-IS) version 2.1](https://www.nist.gov/publications/government-smart-card-interoperability-specification-version-21). Some developer resources can be found on the [CAC developer resources page](https://www.cac.mil/Common-Access-Card/Developer-Resources/).

The standard is implemented in the `src/libopensc/{pkcs15,card}-cac.c`.

Additionally, the older version of the CAC specification CACv1 is implemented in the `src/libopensc/card-cac1.c`. This specification is not available for download, but it is still needed for legacy smart cards or so called CAC Alt tokens (Alternative tokens), which do not support all the specification, but usually provide only limited list of certificates.
