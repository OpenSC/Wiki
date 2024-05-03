# Italian signature card Actalis

Actalis S.p.A. has been bought by Aruba S.p.A. in 2009, but it is still active and operating under the well-established Actalis brand.
It issues chiefly two kinds of smart cards, both manufactured by Athena:

* CNS-compatible smart cards, running a special CNS version of the operating system.
  * These cards may have Athena's ASEPKCS dedicated file, but they support the CNS's own command set.  They should be automatically recognized and handled correctly by the [ASEPCOS](Athena-ASEPCOS-ASEKey) respectively [ItalianCNS](Italian-CNS-and-CIE) driver;.
* JavaCard-based IDProtect XL cards.
  * These specific cards have not yet been tested with OpenSC.

In the past, Actalis has branded and issued another kind of signature card. It may bear the customer's brand and a serial number starting with the letter 'H', followed by some digits. They are CardOS cards and OpenSC should support them correctly with the _cardos_ card driver and the _actalis_ PKCS#15 emulation.
