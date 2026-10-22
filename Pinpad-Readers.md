# Pinpad Readers

OpenSC supports two types of pinpad readers: PC/SC and CT-API.

PC/SC functionality is based on [PC/SC v2 part 10 v2.02.05](https://pcscworkgroup.com/Download/Specifications/pcsc10_v2.02.01_sup.pdf) and is supported by drivers on Windows (check your hardware manufacturer for latest drivers), Linux and Mac OS X Leopard with the [open source CCID driver](https://github.com/LudovicRousseau/CCID). Make sure that you have the latest released versions (or SVN snapshots) of all relevant software: operating system, pcsc-lite, reader driver, reader firmware.

CT-API drivers are most used and available on Windows.

Pinpads tend to be buggy and not all combinations of cards and readers and PIN formats or pinpad reader features (such as displays) have been tested so far, so make sure you report issues.

## Known and tested pinpad readers with  [CT-API](Using-pinpad-readers-with-CT-API) drivers on Windows

> Please feel free to add your hardware and experiences here.

Class 2 readers have a pinpad for secure pin entry. Sometimes they are plugged between computer and keyboard so they use the keyboard for pin entry but capture the keystrokes before they reach the computer.

Class 3 readers have pinpad and a display.

| Reader | OS | Type | CT-API library | Comments |
| ------ | -- | ---- | -------------- | -------- |
| Smartboard G83-6700 | Win32 | Class2 PS/2 | CTMGR.DLL | A keyboard integrated reader which uses the keyboard for pin entry. Buggy CT-API driver, I got it working but not without patching OpenCT. No known Unix support |
| Gemalto | | | | |
| IDBridge CT700/710 (PC Pinpad) | Win32 & Linux | Class3 USB | PC/SC, ?? | CCID compliant. No special actions was needed to get it working on Linux and Win32. (Don't know if it's important - on Windows 'Gemalto Access' middleware was installed. --VTA) |
| Reiner SCT | | | | |
| cyberJack keyboard | Win32 | Class2 PS/2 | CTRSCT32.DLL | A cheap class 2 solution. It uses the keyboard for pin entry. No known Unix support. |
| cyberJack pinpad | Win32 | Class2 USB | CTRSCT32.DLL | cyberJack {basic, pinpad, e-com 2.0} are [discontinued](https://www.reiner-sct.com/lang/en/alte-Treiber.html) |
| cyberJack pinpad | Mac | Class2 USB | with pcscd | If you start the pcscd per hand it works well with the Drivers provided by Reiner SCT. I haven't managed to entry the pin with the Reader's keyboard |
| cyberJack pinpad | Linux | Class2 USB | Reiner SCT CT-API driver | Class 2 Smartcard reader with official LGPL drivers (packages for most linux distributions). Pinpad fully supported (tested on SuSE 10.2). Good Linux support! |
| cyberjack {secoder, one, go plus} | Win, Mac & Linux | Class3 USB | | |
| cyberjack RFID {standard, komfort} | Win, Mac & Linux | Class3 USB | | |
| cyberjack wave | Win, iOS & Android | Class3 USB BT | | |
| SCM" | | | | |
| SPR 332, 532, Chipdrive Pinpad | Win32 & Linux | Class2 USB | CTPCSC32.dll | A widely used CCID compliant reader. I also got it working on Linux following Martin's CardReaders/SPR532 suggestions. |
| STR 391 "CashMouse" | Win32 | Class3 USB | CTRSRW32.dll | Works fine with Win32, no Unix support planned |
| Xiring | | | | |
| XiPass | Win32 | Class3 PS/2 | PC/SC only | Works well with Win32, pinpad entry works with EstEID CSP, no official Unix support, still there is an openct driver for it. |

> Kobil and OmniKey also offer pinpad readers, if someone could test one of those with OpenSC feedback would be appreciated.

## Testing Pinpad

In opensc source you will find `src/tests/pintest` tool. It allows you to test if your card+reader combination
support pinpad.

Before testing pinpad, you may need to erase and initialize pin. Make sure you understand what you are doing, as these commands will erase your smart card:

```bash
pkcs15-init -E
pkcs15-init --create-pkcs15 --profile pkcs15+onepin \
            --use-default-transport-key --pin 0000 --puk 111111 \
            --label "Test"
```

Then run the pin test:

```bash
cd opensc/src/tests;
./pintest
```

The following messages are displayed:

```bash
Using libopensc version 0.12.0-svn.
Card detected in reader 'Feitian SCR301 00 00'
Connecting... connected.
ATR = 3b:9f:95:81:31:fe:9f:00:65:46:53:05:30:06:71:df:00:00:00:81:61:10:c6
Looking for a PKCS#15 compatible Smart Card... found.
Enumerating PIN codes...
PIN [User PIN]
	Com. Flags  : private, modifiable
	Auth ID     : 01
	Flags       : [0x30], initialized, needs-padding
	Length      : min_len:4, max_len:16, stored_len:16
	Pad char    : 0x00
	Reference   : 1
	Encoding    : ASCII-numeric
	Path        : 3f005015
Please enter PIN code [User PIN]: 
```

Enter pin code. You should read:

```bash
PIN code correct.
```

When using a smart card reader with pinpad, you may need to hit return on the computer keyboard and then enter pin on the pinpad reader.
