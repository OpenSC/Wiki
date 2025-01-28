# Compiling on Windows

## Build Requirements

* New Microsoft's [Cryptographic Provider Development Kit](https://www.microsoft.com/en-us/download/details.aspx?id=30688)
* [WiX Toolset](http://wixtoolset.org/)
* [OpenSSL](https://www.openssl.org/) (optional)
* [OpenPACE](https://frankmorgner.github.io/openpace/) (optional)
* [zlib](https://zlib.net/) (optional)

### Getting OpenSSL

Prebuilt OpenSSL libraries are available [here](https://slproweb.com/products/Win32OpenSSL.html). Download and install the 32 bit or 64 bit package for your target platform. OpenSSL can then be found on `C:\OpenSSL-Win32` or `C:\OpenSSL-Win64` respectively.

### Getting OpenPACE

Download and unpack the source code of the [latest release of OpenPACE](https://github.com/frankmorgner/openpace/releases/latest). Open a Visual Studio _Developer Command Prompt_ and change to the OpenPACE's `src` directory. Compile the library:

```powershell
set OPENSSL=C:\OpenSSL-Win64
```

When compiling for 32 bit, use

```powershell
set OPENSSL=C:\OpenSSL-Win32 
```

```powershell
cl /I%OPENSSL%\include /I. /DX509DIR=\"/\" /DCVCDIR=\"/\" /W3 /D_CRT_SECURE_NO_DEPRECATE /DWIN32_LEAN_AND_MEAN /GS /MT /c ca_lib.c cv_cert.c cvc_lookup.c x509_lookup.c eac_asn1.c eac.c eac_ca.c eac_dh.c eac_ecdh.c eac_kdf.c eac_lib.c eac_print.c eac_util.c misc.c pace.c pace_lib.c pace_mappings.c ri.c ri_lib.c ta.c ta_lib.c objects.c
lib /out:libeac.lib ca_lib.obj cv_cert.obj cvc_lookup.obj x509_lookup.obj eac_asn1.obj eac.obj eac_ca.obj eac_dh.obj eac_ecdh.obj eac_kdf.obj eac_lib.obj eac_print.obj eac_util.obj misc.obj pace.obj pace_lib.obj pace_mappings.obj ri.obj ri_lib.obj ta.obj ta_lib.obj objects.obj
```

### Getting zlib

Download and unpack the source code of the latest release of zlib. Open a Visual Studio _Developer Command Prompt_ and change to the zlib directory.

Compile the library for 32 bit:

```powershell
nmake -f win32/Makefile.msc LOC="-DASMV -DASMINF" OBJA="inffas32.obj match686.obj" zlib.lib
```

Compile the library for 64 bit:

```powershell
nmake -f win32/Makefile.msc AS=ml64 LOC="-DASMV -DASMINF -I." OBJA="inffasx64.obj gvmat64.obj inffas8664.obj" zlib.lib
```

### CPDK

When the new CPDK is used, you shall update the Include path:

```diff
--- a/win32/Make.rules.mak
+++ b/win32/Make.rules.mak
@@ -131,7 +131,7 @@ CANDLEFLAGS = -dOpenPACE="$(OPENPACE_DIR)" $(CANDLEFLAGS)


 # Used for MiniDriver
-CNGSDK_INCL_DIR = "/IC:\Program Files (x86)\Microsoft CNG Development Kit\Include"
+CNGSDK_INCL_DIR = "/IC:\Program Files (x86)\Windows Kits\10\Cryptographic Provider Development Kit\Include"
 !IF "$(PROCESSOR_ARCHITECTURE)" == "x86" && "$(PROCESSOR_ARCHITEW6432)" == ""
 CNGSDK_INCL_DIR = "/IC:\Program Files\Microsoft CNG Development Kit\Include"
 !ENDIF</code></pre>
```

## Build Configuration

### Edit `Make.rules.mak`

Change `win32/Make.rules.mak` according to your desired build configuration. Specifically, you may want to change

* `WIX` for WiX Toolset
* `OPENSSL_DEF` for OpenSSL
* `OPENPACE_DEF` and `OPENPACE_DIR` for OpenPACE
* `ZLIBSTATIC_DEF`, `ZLIB_LIB` and `ZLIB_INCL_DIR` for zlib

### Creation of Build Source Files

#### Creating Built Source Files Using Autoconf

Use [Cygwin](https://cygwin.com/install.html) or [MSYS2](http://www.msys2.org/) to install

* autoconf
* automake
* libtool
* make
* gcc or mingw-w64 (will not be used for build)
* pkg-config

Open a Cygwin/MSYS2 terminal or console and change to the OpenSC directory (the previously installed tools need to be found in the `%PATH%`). Create the built source files:

```powershell
autoreconf -i
./configure --disable-openssl --disable-readline --disable-zlib
make -C etc opensc.conf
cp win32/winconfig.h config.h
```

#### Manually Creating Built Source Files

```powershell
copy win32\OpenSC.wxs.in win32\OpenSC.wxs
copy win32\winconfig.h.in win32\winconfig.h
```

The resulting files still contain strings that are encapsulated in @@@. You need to replace these place holders with meaningful values.

## Build OpenSC

Open a Visual Studio _Developer Command Prompt_ and change to the OpenSC directory. Build the OpenSC binaries and installer:

```powershell
nmake /f Makefile.mak
cd win32
nmake /f Makefile.mak OpenSC.msi
```

## Code Signing

Free code signing provided by [SignPath.io](https://about.signpath.io), certificate by [SignPath](https://signpath.org/) Foundation.
