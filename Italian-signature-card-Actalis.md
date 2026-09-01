# Italian signature card Actalis

This page has been merged into **[Italian CNS, CIE and digital signature](Italian-CNS-and-CIE)**.

In short:

* **Legacy Actalis signature cards** — CardOS cards, possibly carrying the customer's brand, whose
  serial number starts with `H` followed by digits. Handled by the `cardos` driver plus the `actalis`
  PKCS#15 emulation, which is a *legacy* emulation and **not enabled by default**; see
  [Italian CNS, CIE and digital signature](Italian-CNS-and-CIE) for the `opensc.conf` setting and for
  what the emulation exposes.
* **Actalis CNS-compatible cards** — these speak the CNS command set (they may also carry Athena's
  ASEPKCS dedicated file) and are handled by the `itacns` or [`asepcos`](Athena-ASEPCOS-ASEKey)
  drivers, not by the `actalis` emulation.
