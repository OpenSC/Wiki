# Smart Card Simulation

This guide uses [jCardSim](https://jcardsim.org/) to simulate the following Open Source Java Card Applets:

- [IsoApplet](https://github.com/philipWendland/IsoApplet)
- [OpenPGP applet](https://github.com/Yubico/ykneo-openpgp)
- [PivApplet](https://github.com/arekinath/PivApplet)
- [GidsApplet](https://github.com/vletoux/GidsApplet)

The description can easily be adapted for other applets as well.

## Simulation on Windows

This section describes how to get the official version of [jCardSim to work through a PC/SC virtual reader](https://jcardsim.org/blogs/work-jcardsim-through-pcsc-virtual-reader) on Windows.

### Prepare the Virtual Smart Card Reader

This needs to be done before all below described applet-specific steps.

1. Install virtual smart card reader: Either use the [original source code](http://www.codeproject.com/Articles/134010/An-UMDF-Driver-for-a-Virtual-Smart-Card-Reader) and follow its manual or use the pre-built installer, [BixVReaderInstaller.msi](https://github.com/frankmorgner/vsmartcard/releases/tag/virtualsmartcard-0.7), from the [Virtual Smart Card](https://frankmorgner.github.io/vsmartcard/virtualsmartcard/README.html) project.

2. Enable the Pipe Reader: Change `C:\Windows\BixVReader.ini` to something like this

    ```text
    [Driver]
    NumReaders=1

    [Reader0]
    RPC_TYPE=0
    VENDOR_NAME=Fabio Ottavi
    VENDOR_IFD_TYPE=Pipe Reader
    DECIVE_UNIT=0
    ```

3. Reload the configuration: In the *Device Manager*, deactivate and activate the *Bix Virtual Smart Card Reader* to load the modification of `BixVReader.ini`. Alternatively, you can use `devcon.exe` from the Windows Driver Kit:

    ```text
    devcon.exe disable root\BixVirtualReader
    devcon.exe enable  root\BixVirtualReader
    ```

4. Download and Install Java (`java.exe` and `javac.exe` are required)
5. Download jCardSim from their [website](https://jcardsim.org/)

Now, [configure jCardSim](https://jcardsim.org/blogs/work-jcardsim-through-pcsc-virtual-reader) to load and run the applet to make it available via PC/SC (see sections below).

### Simulating IsoApplet

1. Download and build IsoApplet:

    ```sh
    git clone https://github.com/philipWendland/IsoApplet
    javac -classpath jcardsim-3.0.4-SNAPSHOT.jar IsoApplet\src\net\pwendland\javacard\pki\isoapplet\*.java
    ```

2. Create `jcardsim_isoapplet.cfg` for IsoApplet:

    ```sh
    com.licel.jcardsim.card.applet.0.AID=F276A288BCFBA69D34F31001
    com.licel.jcardsim.card.applet.0.Class=net.pwendland.javacard.pki.isoapplet.IsoApplet
    com.licel.jcardsim.card.ATR=3B80800101
    ```

3. Run jCardSim with IsoApplet package:

    ```sh
    java -classpath jcardsim-3.0.4-SNAPSHOT.jar;IsoApplet\src com.licel.jcardsim.remote.BixVReaderCard jcardsim_isoapplet.cfg
    ```

4. Create IsoApplet from the install package:

    ```sh
    opensc-tool --card-driver default --send-apdu 80b800001a0cf276a288bcfba69d34f310010cf276a288bcfba69d34f3100100
    ```

5. [Initialize the IsoApplet as usual](https://github.com/philipWendland/IsoApplet/wiki/Initialization)

### Simulating OpenPGP

1. Download and build OpenPGP applet:

    ```sh
    git clone https://github.com/Yubico/ykneo-openpgp
    javac -classpath jcardsim-3.0.4-SNAPSHOT.jar ykneo-openpgp\applet\src\openpgpcard\*.java
    ```

2. Create `jcardsim_openpgp.cfg` for OpenPGP applet:

    ```sh
    com.licel.jcardsim.card.applet.0.AID=D2760001240102000000000000010000
    com.licel.jcardsim.card.applet.0.Class=openpgpcard.OpenPGPApplet
    com.licel.jcardsim.card.ATR=3B80800101
    ```

3. Run jCardSim with OpenPGP:

    ```sh
    javac -classpath jcardsim-3.0.4-SNAPSHOT.jar;ykneo-openpgp\applet\src com.licel.jcardsim.remote.BixVReaderCard jcardsim_openpgp.cfg
    ```

4. Create OpenPGP applet from the install package:

    ```sh
    opensc-tool --card-driver default --send-apdu 80b800002210D276000124010200000000000001000010D276000124010200000000000001000000
    ```

5. [Initialize the OpenPGP applet as usual](https://github.com/OpenSC/OpenSC/wiki/OpenPGP-card#usage)

### Simulating PIV

1. Download and build PivApplet:

    ```sh
    git clone https://github.com/arekinath/PivApplet
    javac -classpath jcardsim-3.0.4-SNAPSHOT.jar PivApplet\src\net\cooperi\pivapplet\*.java
    ```

2. Create `jcardsim_piv.cfg` for OpenPGP applet:

    ```sh
    com.licel.jcardsim.card.applet.0.AID=A000000308000010000100
    com.licel.jcardsim.card.applet.0.Class=net.cooperi.pivapplet.PivApplet
    com.licel.jcardsim.card.ATR=3B80800101
    ```

3. Run jCardSim with PivApplet:

    ```sh
    javac -classpath jcardsim-3.0.4-SNAPSHOT.jar;PivApplet\src com.licel.jcardsim.remote.BixVReaderCard jcardsim_piv.cfg
    ```

4. Create PIV applet from the install package:

    ```sh
    opensc-tool --card-driver default --send-apdu 80b80000120ba000000308000010000100050000020F0F7f
    ```

5. [Initialize the PivApplet as usual](https://github.com/arekinath/PivApplet#installing)

### Simulating GIDS

1. Download and build GidsApplet:

    ```sh
    git clone https://github.com/vletoux/GidsApplet
    javac -classpath jcardsim-3.0.4-SNAPSHOT.jar GidsApplet\src\com\mysmartlogon\gidsApplet\*.java
    ```

2. Create `jcardsim_gids.cfg` for GIDS applet:

    ```sh
    com.licel.jcardsim.card.applet.0.AID=A000000397425446590201
    com.licel.jcardsim.card.applet.0.Class=com.mysmartlogon.gidsApplet.GidsApplet
    com.licel.jcardsim.card.ATR=3B80800101
    ```

3. Run jCardSim with GidsApplet:

    ```sh
    javac -classpath jcardsim-3.0.4-SNAPSHOT.jar;GidsApplet\src com.licel.jcardsim.remote.BixVReaderCard jcardsim_gids.cfg
    ```

4. Create GIDS applet from the install package:

    ```sh
    opensc-tool --card-driver default --send-apdu 80b80000190bA0000003974254465902010bA00000039742544659020100
    ```

5. [Initialize the GidsApplet as usual](https://www.mysmartlogon.com/generic-identity-device-specification-gids-smart-card/)

## Simulation on Linux

On Linux, jCardSim needs to be compiled with support for a different [virtual reader backend (vpcd)](http://frankmorgner.github.io/vsmartcard/virtualsmartcard/README.html)

### Prepare the Virtual Smart Card Reader

This needs to be done before all below described applet-specific steps.

1. Download and build the virtual smart card and its reader driver

    ```sh
    git clone https://github.com/frankmorgner/vsmartcard.git
    cd vsmartcard/virtualsmartcard
    autoreconf -vis && ./configure && sudo make install
    ```

2. Restart `pcscd` to load the new reader driver. On Debian based systems, you could do the following:

    ```sh
    sudo /etc/init.d/pcscd restart;
    ```

### Compile jCardSim with support for vpcd

1. Download the adapted version of jCardSim

    ```sh
    git clone https://github.com/arekinath/jcardsim.git
    ```

2. Fetch Java Card Classic Development Kit

    ```sh
    git clone https://github.com/martinpaljak/oracle_javacard_sdks.git
    export JC_CLASSIC_HOME=$PWD/oracle_javacard_sdks/jc305u3_kit
    ```

3. Build `jcardsim-3.0.5-SNAPSHOT.jar` as described in the [jCardSim build instructions](https://jcardsim.org/docs/getting-source-compiling):

    ```sh
    git clone https://github.com/arekinath/jcardsim.git
    cd jcardsim
    mvn initialize && mvn clean install
    ```

### Simulate Java Card Applet

For actually simulating the Applets, the steps are almost identical as described in the sections for Windows above ([IsoApplet](#simulating-isoapplet), [OpenPGP](#simulating-openpgp), [PIV](#simulating-piv), [GIDS](#simulating-gids)) with the following modifications:

- Make sure to use to use `jcardsim-3.0.5-SNAPSHOT.jar` built for vpcd
- Add the following lines to your jCardSim configuration file (`jcardsim_*.cfg`):

    ```sh
    com.licel.jcardsim.vsmartcard.host=localhost
    com.licel.jcardsim.vsmartcard.port=35963
    ```

- When running jcardsim, use `VSmartCard`, e.g.

    ```sh
    java -classpath 'jcardsim-3.0.5-SNAPSHOT.jar:IsoApplet/src' com.licel.jcardsim.remote.VSmartCard jcardsim_isoapplet.cfg
    ```
