# Overview

> Attention! Information on this page might be outdated or misleading. If in doubt, ask on [Mailing lists](Mailing-lists).

So you want to use a smart card with some application? Here is a small introductions on the parts you need.

* Application
* Smart card library
* Middleware
* Driver for your smart card reader

OpenSC is a **smart card library**. It also comes with tools to manage your smart cards.

## Application Interface

Applications need to use the smart card library using some interface. Unfortunately there
are several different interfaces. PKCS#11 is a standard interface available on all operating
systems. OpenSC implements this interface as smart card library, and Applications such as Mozilla,
Firefox and Thunderbird implement it as applications. Thus you can use these applications with
OpenSC on all platforms.

Native Windows applications often use the Crypto API interface. OpenSC does not implement
the matching interface - CSP - but with the help of an additional software it does. See
WindowsCSP for details. The advantage of Crypto API/CSP is that applications
do not need special code to use smart cards, all applications gain that feature automatically.

Native Mac OS X applications also have a special interface called CDSA or CSP. At this moment
OpenSC does not support that interface
and also there is no bridge to do so. But you can still use applications on Mac OS X
that implement the PKCS#11 interface like Mozilla, Firefox or Thunderbird.

Under Linux, BSD, Solaris and under Unix PKCS#11 is the preferred interface for all
applications.

Some Open Source applications use the non standardized native OpenSC interface directly.
These days we promote PKCS#11 as interface, but for the time being these applications
also work well with OpenSC.

## Driver Interface

Windows implements the PC/SC standard. That means OpenSC will use the PCSC interface to talk
to the middleware, and the middleware will use drivers in "!IfdHandler" format to talk to the
hardware. Nearly all vendors of smart card readers ship such drivers, or the driver is even
included in Windows, so there shouldn't be any issue. OpenSC will be able to talk to your
smart card just fine.

Windows NT/2000/XP and newer include the PC/SC middleware. For older versions you need to install
an addon package from Microsoft first.

Apple/Mac OS X also implements the PC/SC standard, same situation as Windows, except few
vendors ship drivers for Mac OS X, but most smart card readers will work with the generic
driver included in Mac OS X. OpenSC will talk to the reader using the PC/SC Middleware
and thus be able to talk to your smart card just fine.

Apple includes a modified copy of pcsc-lite, an open source implementation of the PC/SC
standard. Most of the time you will be fine, but in some cases it is necessary to install
an updated version of pcsc-lite, for example if you have a smart card reader with a [pinpad](Pinpad-Readers)
and would like to use that capability.

On Linux you might want to use the open source project [OpenCT](https://github.com/OpenSC/openct/wiki)
for smart card drivers. It implements support for many drivers at the same time, is still
small and lean, and OpenSC can use it directly without the need for any middleware.
Many OpenSC developers also work on OpenCT so this combination is best tested. Most Linux
distributions include the latest version of OpenCT.

On Linux you can also use pcsc-lite and drivers in ifdhandler format. Many distributions
include pcsc-lite and some open source drivers, and some vendors also offer binary drivers
for Linux in ifdhandler format.

Solaris situation is like Linux, except Sun has some special stuff for their Sunray
terminals that contain smart card readers. You can use [OpenCT](https://github.com/OpenSC/openct/wiki)
with those terminals; source contains a solaris/ subdirectory with a README files and
additional files to make using OpenCT on solaris easy. OpenCT hides the differences,
so OpenSC works on Solaris well, just like on other platforms.

There is also a very old interface called CT-API which was developed many years ago while
people were using DOS. It only works well if you have a single application with a single
user on your system. It is still being used for specialized machines like ticket point of
sales, but usually not used with modern multi-user, multi-application computers. OpenSC
can use drivers in CT-API format directly, without any middleware, on all operating systems.

## Smart card support

Basically you can get smart card in two states: either blank or initialized.

For blank cards OpenSC has code to initialize the card in PKCS#15 format.

You can't change initialized cards at all, or only with the software that
was used to initialize it. But you can use the card with OpenSC if OpenSC
knows the format. So the format has either to be PKCS#15 (very few
softwares implement that standard, however), or maybe the format was published
and OpenSC contains an emulation for that format.

Check the list on [wiki page](Supported-hardware-(smart-cards-and-USB-tokens)) to see
if your card is supported. Also check the page itself, as some cards have not been tested for a while,
or only some members of a card family are supported.

Also if you want to buy blank cards and initialize them yourself,
make sure you buy really blank cards. Many vendors have also a
half initialized version, and those can be only changed with the
vendor software, and the result is not compatible with OpenSC unless
OpenSC has an emulation code. Even then OpenSC can only offer you to
use the card, but not to alter it.

As a general rule OpenSC only supports cards with a filesystem and
cryptographic functions (RSA). That excludes nearly all Java Cards,
as they usually don't have a filesystem. Please check the
[Musclecard](http://www.musclecard.com/) project - they offer
open source software for using many different Java Cards.

## Concepts

### PKCS Standards

PKCS is the Public Key Cryptography Standard - a series of standards created by RSA Labs. Each standard is about a different topic, they complement each other. See the RSA Labs page for details.

#### PKCS#11 - Cryptoki

PKCS#11, also known as Cryptoki or as "RSA Security Inc. PKCS #11 Cryptographic Token Interface (Cryptoki)". It is an API and ABI standard for writing software that cryptographic hardware such as smart cards or other way to provide cryptography. This standard is implemented by Firefox and Thunderbird on the application side and OpenSC and Muscle on the token side.

Firefox and friends have implemented the standard, so they can load modules in PKCS#11 format (DLLs under Windows, shared objects under Linux / Unix). OpenSC and Muscle implement the standard to provide such a module that can be loaded by these applications.

The standard defines the module interface (ABI and API) for functions like signing, decrypting, listing keys and certificates, PIN handling and so on.

#### PKCS#15 - Cryptographic Token Information Format Standard

PKCS#11 can be implemented in any way - using smart cards, using normal files, using other special hardware. But what if several people implement PKCS#11, even for the same smart card? Will the same card work with both implementations?

The answer to that is: No, unless both implementations use the same structures (like PKCS#15) on the card. It defines a standard how to store keys, certificates (and other objects) on smart cards, manage them and retrieve them again. It defines directory files that link file names to identifiers, labels and flags, so you can see what is on a smart card and what it can be used for and all that.

OpenSC implements PKCS#15 and thus stores everything in the directory 5015, creates certain files in defined formats, subdirectories and so on. Not all software implement PKCS#15, for example Aladdin Knowledge System has their custom software that stores everything in the 6666 directory in a format only known to them. Even if we see there is something we are not sure what it is and how it is meant to be used - unless the format is publicly documented.

Many cards in EU and elsewhere have ID cards for their citizens with keys for digital signatures and authentication, and often those cards and not in PKCS#15 format. But OpenSC implements emulations for a number of other documented formats, so these cards can still be used.

Also note that while all cards created with PKCS#15 compatible software can be used by each other software, that does not include changes to the card - only few small changes like changing the PIN code can be done with cards initialized with other software. Big changes like adding keys, removing keys, replacing certificates and so on will often need the software that was used to initialize it in PKCS#15 format in the first place. So PKCS#15 standard helps only for using cards, not for altering them.

## Middleware and Readers

How do you talk to a smart card and the smart card reader? Using some software provided by the vendor of the smart card reader. But how do you exactly talk to it? Well, it needs to conform to one of the standards, so the application author knows how to access it.

OpenSC on Linux uses all three alternatives provided here, but you can turn off the ones you don't need in the config file (see below).

### PC/SC

PC/SC is the de facto standard for smart card access and is implemented on Windows (the reference implementation) Linux and Mac OS X (pcsc-lite project). Most modern smart card readers support CCID.
For details see [PCSC and pcsc-lite](PCSC-and-pcsc-lite).

### OpenCT

Some developers here didn't like CT-API and PC/SC too much, so we wrote our own code and our own middleware and called it OpenCT. It is no standard, but if you want to write a driver for a smart card reader to be used under Linux, adding a driver to OpenCT might be the best thing you can do. OpenCT has its own API and OpenSC uses it directly.

But we also know that many other applications are written to for CT-API or PC/SC, and thus as a result OpenCT also implements those two alternatives, so applications can use it. But those interfaces are not as much tested as the OpenCT native interface itself.

### CT-API and CT-BCS (deprecated)

CT-API was developed in the 1980ies for DOS. It defines a very simple API, so vendors can ship their smart card readers with some software, and applications authors can use that software, but don't need to know or care what kind of smart card reader he customer will use.

CT-BCS is a sister standard to CT-API. With it application authors can send commands similar to the commands send to smart cards also to the smart card reader. Either the reader or the driver will interpret the command and send an answer. Typical commands would be "is a card in the reader?" or "please reset the card". Drivers in CT-API format implement both CT-API and CT-BCS at the same time, so don't worry if you see only CT-API.

The CT-API is very limited and meant for machines with one user only and one application only and thus doesn't fit well into todays world with many applications running at the same time and maybe even several users running software on the same computer at the same time.
