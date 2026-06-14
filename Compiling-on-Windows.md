# Compiling on Windows

## Build Requirements

* Microsoft's [Cryptographic Provider Development Kit](https://www.microsoft.com/en-us/download/details.aspx?id=30688)
* [vcpkg](https://vcpkg.io/)

## Setup

Install WiX:

```powershell
powershell -ExecutionPolicy ByPass -File win32\setup-wix.ps1
```

Install dependencies via vcpkg:

```powershell
$env:VCPKG_INSTALLED = "vcpkg_installed"
$env:VCPKG_DEFAULT_TRIPLET = "x64-windows-static"
vcpkg install `
  --overlay-ports win32/ports `
  --x-feature=zlib `
  --x-feature=openpace `
  --x-manifest-root win32 `
  --x-install-root $env:VCPKG_INSTALLED
```

Omit --x-feature flags for features you don't need or skip this step if you do not need OpenSSL.

## Build OpenSC

Open a Visual Studio _Developer Command Prompt_ and change to the OpenSC directory. Build the OpenSC binaries and installer:

```powershell
nmake /f Makefile.mak OpenSC.msi
```

## Code Signing

Free code signing provided by [SignPath.io](https://about.signpath.io), certificate by [SignPath](https://signpath.org/) Foundation.
