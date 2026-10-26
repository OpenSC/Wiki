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
$env:VCPKG_INSTALLED = "$PWD\vcpkg_installed"
vcpkg install `
  --overlay-ports win32/ports `
  --triplet x64-windows-static `
  --x-feature=zlib `
  --x-feature=openpace `
  --x-feature=tests `
  --x-manifest-root win32 `
  --x-install-root $env:VCPKG_INSTALLED
```

Omit --x-feature flags for features you don't need or skip this step if you do not need OpenSSL.
The `tests` feature installs cmocka, which enables building the unit tests.

## Build OpenSC

Open a Visual Studio _Developer Command Prompt_ and change to the OpenSC directory. Build the OpenSC binaries and installer:

```powershell
nmake /f Makefile.mak OpenSC.msi
```

## Run Unit Tests

Requires the `tests` vcpkg feature. Build and run the unit tests:

```powershell
nmake /f Makefile.mak check
```

## Code Signing

Free code signing provided by [SignPath.io](https://about.signpath.io), certificate by [SignPath](https://signpath.org/) Foundation.
