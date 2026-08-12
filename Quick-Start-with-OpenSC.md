# Quick Start with OpenSC

If you haven't already, please first take a look at our [[Overview|Overview]] page, the [[Operating Systems|Recent-test-results-for-various-smart-cards]] page and the [[Compiling and Installing on Unix flavors]] page.

## Before we start

A word of warning: these experiments can destroy your card (e.g. if we have a bug. there is **NO WARRANTY** on opensc of any kind).  Also be sure to make notes of everything you do, *especially*< the pin and puk and so-pin and so-puk you set, as it is not possible to erase some cards without these!

## Install the required middleware

Some older card readers (or standalone USB tokens) use a nonstandard wire format for communicating between the computer and the device. You will need to get the corresponding (often proprietary) software up and running first.  For USB tokens see the respective page on this Wiki (e.g., [SafeNet tokens](SafeNet-cards)).  For card readers, you should get to the point where the LED turns on when you plug it into the USB socket.

## Install OpenSC

For Mac OS X, download and install SCA.

For Windows, visit the build project.

For Linux, either use your distribution's package manager or see [[Compiling and Installing on Unix flavors]].

## Test OpenSC

First check if your smart card reader is found:

```sh
$ opensc-tool --list-readers
Readers known about:
Nr.    Driver     Name
0      openct     Towitoko Chipdrive Micro
1      openct     Aladdin eToken PRO
2      openct     OpenCT reader (detached)
3      openct     OpenCT reader (detached)
4      openct     OpenCT reader (detached)
```

You can see, openct claims five slots, but only two are used. This is done to support hotplugging, those slots can be filled later by additional readers you plugin via usb.

Next test is to see if your card is found. Every card has a so called ATR ("Answer to reset"), a hex string used for identifying the card type.

```sh
$ opensc-tool --reader 0 --atr
3b:e2:00:ff:c1:10:31:fe:55:c8:02:9c
```sh

Lets see if that card is supported by OpenSC. If so, we should know the name of the card:

```sh
$ opensc-tool --reader 0 --name
Cryptoflex 32K e-gate
```

OpenSC has a small low level tool for exploring your smart card. This is useful if you have a new card and want to look at it, or check some details.

```sh
$ opensc-explorer
```

However `opensc-explorer` only works with known cards and even then: some cards don't have then required functionality, for example no `ls` command.

## Quick start guide to initializing a blank card

The best way to use all features of OpenSC is to start with a blank card and initialize it with OpenSC. Make sure your vendor sold you a real blank card, many vendors also have pre-initialized cards, and those only work with the vendors software, but not or only limited with OpenSC.

**Warning:** before writing any data on the token please read the smartcard os specific wiki pages as some smartcards cannot be deleted once initialized.

You can add `-v` to all of these commands, to get a more verbose output. Adding `-v` more than once will enable debugging or increase the debugging level.

First you need to create the basic structure. At this step you are asked to enter a "security office" pin. Only with this pin you can alter the card, but that pin is not needed to use the keys.

```sh
$ pkcs15-init --create-pkcs15
New Security Officer PIN (Optional - press return for no PIN).
Please enter Security Officer PIN: 
Please type again to verify: 
Unblock Code for New User PIN (Optional - press return for no PIN).
Please enter User unblocking PIN (PUK): 
Please type again to verify:
```

Next step is to create a user and a pin. That pin is needed for using the keys we will create later.

```sh
$ pkcs15-init --store-pin --auth-id 01 --label "Andreas Jellinghaus"
New User PIN.
Please enter User PIN: 
Please type again to verify: 
Unblock Code for New User PIN (Optional - press return for no PIN).
Please enter User unblocking PIN (PUK): 
Please type again to verify: 
Security officer PIN required.
Please enter Security officer PIN: 
```

Now create a key. Both pins are needed for this.

```sh
$ pkcs15-init --generate-key rsa/1024 --auth-id 01
Security officer PIN required.
Please enter Security officer PIN: 
User PIN required.
Please enter User PIN: 
Security officer PIN required.
Please enter Security officer PIN: 
```

You can list the keys on the token with

```sh
$ pkcs15-tool --list-keys
Private RSA Key [Private Key]
        Com. Flags  : 3
        Usage       : [0x4], sign
        Access Flags: [0x1D], sensitive, alwaysSensitive, neverExtract, local
        ModLength   : 1024
        Key ref     : 16
        Native      : yes
        Path        : 3F005015
        Auth ID     : 01
        ID          : 45
```

## Testing using OpenSSL

If you followed thus far, your token is now fitted with a private RSA key that it generated itself and never divulged to anybody (not even the host computer).  Assuming [engine_pkcs11](https://github.com/OpenSC/libp11) is installed, we can use this key and openssl to create a self signed certificate, still without divulging the key; the necessary cryptographic computations will occur on-token.

Let's start the OpenSSL interactive shell and load the [engine_pkcs11](https://github.com/OpenSC/libp11) so that OpenSSL can ask the token to do the crypto (as opposed to doing it from your computer's CPU).

* **Linux**: open a terminal and type this (skipping the prompts):

```sh
$ openssl
OpenSSL> engine dynamic -pre SO_PATH:/usr/lib/engines/engine_pkcs11.so -pre ID:pkcs11 -pre LIST_ADD:1 -pre LOAD -pre MODULE_PATH:opensc-pkcs11.so
```

* **Mac OS X**: open a terminal and type this (skipping the prompts):

```sh
$ /Library/OpenSC/bin/openssl
OpenSSL> engine dynamic -pre SO_PATH:/Library/OpenSC/lib/engines/engine_pkcs11.so -pre ID:pkcs11 -pre LIST_ADD:1 -pre LOAD -pre MODULE_PATH:/usr/lib/opensc-pkcs11.so
```

In both cases, OpenSSL should respond with something like

```sh
(dynamic) Dynamic engine loading support
[Success]: SO_PATH:/usr/lib/engines/engine_pkcs11.so
[Success]: ID:pkcs11
[Success]: LIST_ADD:1
[Success]: LOAD
Loaded: (pkcs11) pkcs11 engine
OpenSSL> 
```

It is important to enter the whole long command in one single command
line. I usually copy&paste the command, to make sure I don't mistype
anything.

Staying at the OpenSSL prompt, now type:

```sh
OpenSSL> req -engine pkcs11 -new -key id_45 -keyform engine -x509 -out cert.pem -text
SmartCard PIN: 
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:.
State or Province Name (full name) [Some-State]:.
Locality Name (eg, city) []:.
Organization Name (eg, company) [Internet Widgits Pty Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, YOUR name) []:Andreas Jellinghaus
Email Address []:aj@dungeon.inka.de

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
OpenSSL> 
```

This creates a signed certificate as file `cert.pem` (again, without divulging the private key).  You can verify that it is indeed self-signed (the private key is not required for this): exit OpenSSL and type

```sh
$ openssl verify -CAfile cert.pem cert.pem
cert.pem: OK
```

If instead you remove the "-x509" flag in the `req` OpenSSL command, you get a certificate signing request. Send it to the CA, wait till you get it back, signed, and proceed.

Now we can store the certificate side by side with the key on the token, as a piece of public (but read-only) data. It is important to save the certificate under the same ID as the key, so that applications wanting to use that certificate on your behalf can find the private key as well. You can get a list of all keys and their details (including the ID) with:

```sh
$ pkcs15-tool --list-keys
Private RSA Key [Private Key]
        Com. Flags  : 3
        Usage       : [0x4], sign
        Access Flags: [0x1D], sensitive, alwaysSensitive, neverExtract, local
        ModLength   : 1024
        Key ref     : 16
        Native      : yes
        Path        : 3F005015
        Auth ID     : 01
        ID          : 45
```

So lets store the certificate that we created:

```sh
$ pkcs15-init --store-certificate cert.pem --auth-id 01 --id 45 --format pem 
Security officer PIN required.
Please enter Security officer PIN: 
```

Now we are ready to go. If you want to add more certificates (e.g. the root certificate of the CA that signed your key, or some intermediate certificates in the chain to the root CA) simply put those into pem files, and add them to id 46, 47 and so on.  You don't need the private key for these obviously.

## Now what?

You probably want to make your token work with other applications than `pkcs15-init` and OpenSSL: see [Application Support](Using-smart-cards-with-applications).

If you want to login to your computer with your smart card or crypto token, please note that OpenSC 0.10 does not include the pam module and the openssl engine any more. We suggest you install [libp11](https://github.com/OpenSC/libp11) and one of [pam_p11](https://github.com/OpenSC/pam_p11) (a simple authentication module) or [pam_pkcs11](https://github.com/OpenSC/pam_pkcs11) (a full featured authentication module).
