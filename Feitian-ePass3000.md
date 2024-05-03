# Feitian ePass3000

[Feitian](http://www.ftsafe.com/) used to offer the ePass3000, an USB crypto token with 32-bit high performance smart card chip and support for accelerated hardware computation. It is now superseded by [ePass2003](Feitian-ePass2003).

The driver of ePass3000 in OpenSC is called "entersafe".

Feitian has their own software for Windows, GNU/linux and MAC OSX. This software does not implement PKCS15 and thus is not compatible with OpenSC. Because Feitian's software reserves all storage, its data cannot be co-existed with OpenSC's in the USB token. In addition, there may be unexpected errors if both softwares exists in the operating system concurrently, since Feitian's software assumes there is one and only one software manipulates the token.

Token initialized with Feitian's private format can not be directly used by OpenSC. Unless it is totally erased by command:

```bash
pkcs15-init -E
```

(all data including private keys inside the token will be lost)
Then the token can be re-initialized to OpenSC format by command:

```bash
pkcs15-init -p pkcs15+onepin -C
```

The APDU level manual and further documentation to implement OpenSC are available only under NDA as far as we know.

The USB Interface of the ePass3000 is not public.
Recent OpenCT also [has support](https://github.com/OpenSC/openct/wiki/Feitian-ePass3000) for this token.

The ePass3000 is now superseded by the [ePass2003](Feitian-ePass2003), which offers a CCID interface and therefore requires no proprietary software, altogether on one single chip with a weight of only 6 grams.

## Thanks

Big thanks to EnterSafe division of [Feitian](http://www.ftsafe.com/), for their technical help in adding support for the ePass3000, and donating hardware tokens.
