# MacOS Quick Start

1. Download the DMG

    Download the [latest release of OpenSC](https://github.com/OpenSC/OpenSC/releases/latest).

2. Install the PKG

    Opening the DMG-file loads the OpenSC bundle into Finder. Now double-click the installer `OpenSC*.pkg`.

    A Custom Setup allows disabling some of the following features:

    - *PKCS#11 module and smart card tools*: PKCS#11 module used by most open source and cross-platform software (like Firefox, SSH, TrueCrypt, OpenVPN etc) as well as tools for debugging and personalization.
    - *Automatic startup items*: Registers PKCS#11 module and notification capabilities on startup
    - *CryptoTokenKit-based smart card driver*: OpenSC CTK plugin for using smart cards with native macOS applications (like Safari, iMail, Chrome, `sc_auth` etc)

3. Test your installation

    Upon successful installation, OpenSC is installed in `/Library/OpenSC`, the *CryptoTokenKit* module was registered and links to the OpenSC tools have been created in `/usr/local/bin`.

    The PKCS#11 modules have been installed as `/Library/OpenSC/lib/opensc-pkcs11.so` and `/Library/OpenSC/lib/onepin-opensc-pkcs11.so` (copies of the libraries are available in `/usr/local/lib`).

    You may test the CryptoTokenKit support of your card with

    ```bash
    # installation details of the CryptoTokenKit plugin 
    pluginkit -v -m -D -i org.opensc-project.mac.opensctoken.OpenSCTokenApp.OpenSCToken
    # list your usable identities (token certificates)
    sc_auth identities
    ```

    You may test the PKCS#11 support of your card with

    ```bash
    /Library/OpenSC/bin/pkcs11-tool --login --test
    ```

4. Customize your configuration

    Change the default configuration file `/Library/OpenSC/etc/opensc.conf` to your needs.

5. Uninstall OpenSC

    From the OpenSC bundle double click the *OpenSC Uninstaller*. Alternatively, run the following from the command line:

    ```bash
    sudo opensc-uninstall
    ```

6. Code Signing

    Code signing is fully integrated with the automatic build process for OpenSC releases and nightly builds. We are grateful to Raul Metsma, who as member of Apple's developer program is signing the OpenSC binaries.
