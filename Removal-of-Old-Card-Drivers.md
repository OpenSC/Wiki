# Removal of Old Card drivers

Many of the internal card drivers have not been modified or used in years (`card-*.c` and their `pkcs15-*.c` counterpart). Although they may have gotten touched due to general security fixes, these changes are mostly untested with these card drivers. Most likely these cards are not in use anymore. To reduce the overall attack surface, we are planning to remove the old card drivers.

## Card Driver Overview

| Driver | Support Added | To be Deactivated | Removed | User Activity | Developer Activity |
| -------------------- | ---------------------------------------------------------------------------------------- | ----------------- | ------------- | ---------------------------------------------- | ---------------------------------------------------------------------------------------- |
| `card-akis.c` | [2007](https://github.com/OpenSC/OpenSC/commit/60e3e1e6832fc04e67276a513e0cb591b0dc2583) | | yes | | [2007](https://github.com/OpenSC/OpenSC/commit/f9476144182dcc1568518f436ec8e5368841902a) |
| `card-asepcos.c` | [2007](https://github.com/OpenSC/OpenSC/commit/87708593154ad96ac9e8d8dec5d066e0387db0b2) | yes | no | [2023](https://github.com/OpenSC/OpenSC/issues/2832) | [2014](https://github.com/OpenSC/OpenSC/commit/b1b5a39ffad95814901d1955360555d9b3608c5e) |
| `card-authentic.c` | [2010](https://github.com/OpenSC/OpenSC/commit/1a58c429cbed13bed30e561896e747e3051a6d15) | no | no | [2023](https://github.com/OpenSC/OpenSC/issues/2693) | [2018](https://github.com/OpenSC/OpenSC/commit/098eed146f50f0375bd41d70116f9dd9ebc4dde4) |
| `card-atrust-acos.c` | [2005](https://github.com/OpenSC/OpenSC/commit/cbb32e02e13f77d65790279794a17895336cbcae) | yes | no | | [2020](https://github.com/OpenSC/OpenSC/commit/723129bc123699ef72332ed106628236b96bd20d) |
| `card-belpic.c` | [2005](https://github.com/OpenSC/OpenSC/commit/a6ba20a1e3d00b8cf0049c1f7b48b819f0b2d5b4) | no | no | [2021](https://github.com/OpenSC/OpenSC/issues/2305) | [2021](https://github.com/OpenSC/OpenSC/commit/9d6ddaef13472cb7aaafc1b418937493f44bc422) |
| `card-cac.c` | [2017](https://github.com/OpenSC/OpenSC/commit/777e2a3751e3f6d53f056c98e9e20e42af674fb1) | no | no | [2021](https://github.com/OpenSC/OpenSC/issues/2386) | [2020](https://github.com/OpenSC/OpenSC/commit/704afd0e2ddbaceb96d3861579244358b6eef326) |
| `card-cac1.c` | [2018](https://github.com/Jakuje/OpenSC/commit/90fbc57f75585a1faed9175260559c1afffe0bcb) | no | no | | |
| `card-cardos.c` | [2006](https://github.com/OpenSC/OpenSC/commit/823bbffbe65f1511849428637cd216c82ddbac2c) | no | no | [2023](https://github.com/OpenSC/OpenSC/issues/2784) | [2021](https://github.com/OpenSC/OpenSC/commit/d6ec00c8707d565df156eaefe54377105a51c72c) |
| `card-coolkey.c` | [2016](https://github.com/OpenSC/OpenSC/commit/790ed5fcd7a3189c2df686daebe02f43ae5a72c3) | no | no | | [2018](https://github.com/OpenSC/OpenSC/commit/abdbb9d8c09146cd0bfbb5ef8861c1f3a14a83c7) |
| `card-dnie.c` | [2013](https://github.com/OpenSC/OpenSC/commit/63ce563d76f0c4003b248654f09dd9b2af1b1069) | no | no | [2020](https://github.com/OpenSC/OpenSC/issues/2258) | [2020](https://github.com/OpenSC/OpenSC/commit/57a391f16cd69cedf02d5b326a689d19a985de11) |
| `card-edo.c` | [2020](https://github.com/OpenSC/OpenSC/commit/d4a9405bf4e9ca4c1abecc9307c0b75664fc9639) | no | no | [2019](https://github.com/OpenSC/OpenSC/issues/1831) | |
| `card-entersafe.c` | [2008](https://github.com/OpenSC/OpenSC/commit/27d00f9265e053c574690d9cf27d4d579d29a09d) | no | no | [2023](https://github.com/OpenSC/OpenSC/issues/2815) | [2017](https://github.com/OpenSC/OpenSC/commit/45e1732bb5b020ceaf1892527c784294b28b67a8) |
| `card-eoi.c` | [2023](https://github.com/OpenSC/OpenSC/commit/4340b757e0d93e5593f8a6062ed2b93b38f3e303) | no | no | [2022](https://github.com/OpenSC/OpenSC/issues/2564) | |
| `card-epass2003.c` | [2012](https://github.com/OpenSC/OpenSC/commit/00b069a08e43397e02571674d105c38b5967f459) | no | no | [2023](https://github.com/OpenSC/OpenSC/issues/2856) | [2023](https://github.com/OpenSC/OpenSC/commit/6341c1984c043dbd68dd7d62b743c02f3534301b) |
| `card-esteid2018.c` | [2019](https://github.com/OpenSC/OpenSC/commit/b3d4a0d69a8f56c417e6ee141b57d358f7f58163) | no | no | [2021](https://github.com/OpenSC/OpenSC/issues/2429) | |
| `card-flex.c` | [2002](https://github.com/OpenSC/OpenSC/commit/9dc705491907727b554cf11eb4124b55ecdb9ea4) | yes | no | | [2010](https://github.com/OpenSC/OpenSC/commit/7d935df1bc65022ef80a40f8721f0fa8e3709289) |
| `card-gemsafeV1.c` | [2007](https://github.com/OpenSC/OpenSC/commit/07e6b23a266f051666aa375b5cb6d43ad2d9cb3a) | no | no | [2019](https://github.com/OpenSC/OpenSC/issues/1871) | [2019](https://github.com/OpenSC/OpenSC/pull/1872/commits/ba3108d1bd6368e410243a34ed6cfa94ad78f32a) |
| `card-gids.c` | [2016](https://github.com/OpenSC/OpenSC/commit/6a243449a0169ac7722408c23f1fd04f44b03877) | no | no | [2020](https://github.com/OpenSC/OpenSC/issues/2084) | [2019](https://github.com/OpenSC/OpenSC/commit/b59456b6e4fdcdbc1be31b70ad94ac61f5f5b8f8) |
| `card-gpk.c` | [2002](https://github.com/OpenSC/OpenSC/commit/b4063302bf694f579aec1e83f6bcb7d13189e842) | | yes | | [2007](https://github.com/OpenSC/OpenSC/commit/a8908b8548e02d320376844b3d9668f0f89b3c29) |
| `card-iasecc.c` | [2011](https://github.com/OpenSC/OpenSC/commit/57b7a81c3a3233e93e9b60927103b26f9a20463e) | no | no | [2021](https://github.com/OpenSC/OpenSC/issues/2303) | [2021](https://github.com/OpenSC/OpenSC/pull/2279/commits/59cd37f38737e31583694da23cbc12a7fd25421d) |
| `card-idprime.c` | [2020](https://github.com/OpenSC/OpenSC/commit/f61d9b3b53aa97c3dad6f8d065c989786662878a) | no | no | [2022](https://github.com/OpenSC/OpenSC/issues/2589) | [2023](https://github.com/OpenSC/OpenSC/commit/c20e070ba299ec0302169aa22443daf67a5fe56e) |
| `card-incrypto34.c` | [2005](https://github.com/OpenSC/OpenSC/commit/90d1b0cc9c2ad226e07ce4d811d29f5f6409ee63) | | yes | | [2007](https://github.com/OpenSC/OpenSC/commit/a2f622a21521cb350541894d80e3266b5f2f5612) |
| `card-isoApplet.c` | [2015](https://github.com/OpenSC/OpenSC/commit/48bd6b09645014365a2be8361d113757a1736814) | no | no | [2023](https://github.com/OpenSC/OpenSC/issues/2704) | [2023](https://github.com/OpenSC/OpenSC/commit/84320d2f6d2b05e54286ca565617d010d73a2adb) |
| `card-itacns.c` | [2010](https://github.com/OpenSC/OpenSC/commit/71cdef0ed255b63f34246960d3c9c36c2d2f8989) | no | no | [2023](https://github.com/OpenSC/OpenSC/issues/2763) | [2022](https://github.com/OpenSC/OpenSC/commit/1708c065f1ff2f754e9756de69ab3a3139a255bb) |
| `card-jcop.c` | [2003](https://github.com/OpenSC/OpenSC/commit/8a4a5f033107ce0e3e8224a62a7e86dd3191f2c3) | | yes | | [2003](https://github.com/OpenSC/OpenSC/commit/f761d1504fa0d173c79df30d9ef64aacf2909a2b) |
| `card-jpki.c` | [2016](https://github.com/OpenSC/OpenSC/commit/33a16b4f4e6ad7de3eb223aeccb12510c0c4145f) | no | no | | [2017](https://github.com/OpenSC/OpenSC/commit/51ba288becc8b8040077ed248ba91c94f020b805) |
| `card-masktech.c` | [2015](https://github.com/OpenSC/OpenSC/commit/976db5cb0400147e9108d434796d68c27f7b4c5c) | no | no | | [2015](https://github.com/OpenSC/OpenSC/commit/56c376489f4544d3f09bb71de675621ca51e6b12) |
| `card-mcrd.c` | [2002](https://github.com/OpenSC/OpenSC/commit/8bbcb933a484f1a06453494f7aa0b33e4d694526) | yes | no | [2017](https://github.com/OpenSC/OpenSC/issues/943) | [EstEID deactivated in 2024](https://github.com/OpenSC/OpenSC/commit/7c19a920d7c94efa3695967b61d6981900503218) |
| `card-miocos.c` | [2002](https://github.com/OpenSC/OpenSC/commit/04dfc47d92ebfbccce826c4fcee970ea7f69c466) | | yes | | [2003](https://github.com/OpenSC/OpenSC/commit/30c094395aeea1cfc20b4c0a25dd8107ef809fd3) |
| `card-muscle.c` | [2006](https://github.com/OpenSC/OpenSC/commit/50490acca0c32b37c71b8e86748648b7a2811ade) | no | no | | [2015](https://github.com/OpenSC/OpenSC/commit/5898eab3732f73550b36b6dae4a947dfd0c6f4e2) |
| `card-myeid.c` | [2009](https://github.com/OpenSC/OpenSC/commit/9da39d84629fe22492989b640fc9f7fcc30bb4fc) | no | no | [2023](https://github.com/OpenSC/OpenSC/issues/2804) | [2023](https://github.com/OpenSC/OpenSC/commit/d5e7459a3580a5486b43fffb66bd1aabddf5f504) |
| `card-npa.c` | [2017](https://github.com/OpenSC/OpenSC/commit/a4f64d9439cf072e2c5ef0f8dd1cfd508b75242a) | no | no | [2023](https://github.com/OpenSC/OpenSC/issues/2726) | [2022](https://github.com/OpenSC/OpenSC/commit/0a7be07a715aa2589250d4fe3a0f953f5b5e4976) |
| `card-nqApplet.c` | [2021](https://github.com/OpenSC/OpenSC/commit/2d183fe66262588941e7fb77f9f84264be1120ba) | no | no | | |
| `card-oberthur.c` | [2004](https://github.com/OpenSC/OpenSC/commit/52c1b2d700f4d41b3bf04a76f94ce46a06f4898f) | no | no | [2020](https://github.com/OpenSC/OpenSC/issues/2047) | [2016](https://github.com/OpenSC/OpenSC/commit/e9786bfb345f9907ae1007f80d94f6eb3f773d59) |
| `card-openpgp.c` | [2003](https://github.com/OpenSC/OpenSC/commit/0f32616c203ea38bccba235951d24642715dd2f1) | no | no | [2023](https://github.com/OpenSC/OpenSC/issues/2824) | [2023](https://github.com/OpenSC/OpenSC/commit/4e9f4e0ed2955b4ab52f829309ef51d8900564f9) |
| `card-piv.c` | [2006](https://github.com/OpenSC/OpenSC/commit/b051b085846967795299e4bb92e19e1028b1048c) | no | no | [2023](https://github.com/OpenSC/OpenSC/issues/2833) | [2023](https://github.com/OpenSC/OpenSC/commit/dfeeac68f9cc3b26fe434ef7cfe046905a54b1d5) |
| `card-rtecp.c` | [2009](https://github.com/OpenSC/OpenSC/commit/7931ded481170704ca047ae5442e5a6b2a299926) | no | no | [2017](https://github.com/OpenSC/OpenSC/issues/1082) | [2021](https://github.com/OpenSC/OpenSC/commit/570fc56c47524b9a23beddc0ab7426a31491c4ff) |
| `card-rutoken.c` | [2007](https://github.com/OpenSC/OpenSC/commit/0ecc294ed8bea00c712e90b0db3ea29eef935730) | no | no | | [2019](https://github.com/OpenSC/OpenSC/commit/3c1624676d486c951cc101a32a5b20ec31e2b52f) |
| `card-sc-hsm.c` | [2012](https://github.com/OpenSC/OpenSC/commit/1a7ca328659fdcd11a4755dcab5645f9a2b6dff8) | no | no | [2023](https://github.com/OpenSC/OpenSC/issues/2826) | [2023](https://github.com/OpenSC/OpenSC/commit/a5b4950c623d465ea46a06856cdcd55d0a8d0820) |
| `card-setcos.c` | [2001](https://github.com/OpenSC/OpenSC/commit/c344e28a9269353884cb7082a4f9830ea6a74bed) | no | no | [2017](https://github.com/OpenSC/OpenSC/issues/1009) | [2016](https://github.com/OpenSC/OpenSC/commit/74493ca73f8e3c21c098fecb42a7a08ead85e197) |
| `card-skeid.c` | [2023](https://github.com/OpenSC/OpenSC/commit/d952a9fa3d0e2becc684c3a2529173d3d151f817) | no | no | | |
| `card-starcos.c` | [2003](https://github.com/OpenSC/OpenSC/commit/ac1c926cc828f577950d46b872763f6a4b45b3fc) | no | no | [2021](https://github.com/OpenSC/OpenSC/issues/2460) | [2022](https://github.com/OpenSC/OpenSC/commit/9ac0583e91b1b59bd9dc55b2376ff0be944afe55) |
| `card-tcos.c` | [2002](https://github.com/OpenSC/OpenSC/commit/709727d469954f09f96492a219ecb60b334a62a2) | no | no | [2019](https://github.com/OpenSC/OpenSC/issues/1869) | [2019](https://github.com/OpenSC/OpenSC/commit/187d908feb5867a7d44604e02c02c2131285f74b) |
| `card-westcos.c` | [2009](https://github.com/OpenSC/OpenSC/commit/d3201511c7efc5d30c9105a1c92dac63f4af4808) | | yes | | [2010](https://github.com/OpenSC/OpenSC/commit/c3de15d2d08061cd6b2a0fabbdaaa7b8a6ede1fa) |

**Deactivated**: The card driver is removed from the default OpenSC configuration. For usage, it is necessary to enable the card driver in `opensc.conf`

**Removed**: The card driver is removed from the default OpenSC binaries. For further use, the drivers need to be pulled from git history.

**User Activity**: Indicates if someone is actively using the card. This may be a bug report, feature request, a question on the mailing list.

**Developer Activity**: Indicates if someone is actively developing and maintaining a card driver. This doesn't include generic fixes, which are untested with the card in question, e.g. fixes for issues that are reported by Coverity scan.

## Rationale for Removing a card driver

* A card driver will be deactivated if there was no activity for 7 years or more
* A card driver will be removed if there was no activity for 10 years or more
* Removing a card driver will take at least two release cycles (i.e. one release that deactivates the card driver and a second to remove it)
