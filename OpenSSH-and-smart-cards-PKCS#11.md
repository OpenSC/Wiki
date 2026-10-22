# OpenSSH and smart cards / PKCS#11

[OpenSSH](http://www.openssh.org/) can be used with client keys on a smart card. There are three different methods.

## Best solution since v5.4p1

Portable OpenSSH v5.4p1 added direct support for modules.

Basic usage looks like this:

```bash
$ ssh -I /usr/lib/opensc-pkcs11.so martin@remotehost
Enter PIN for 'MARTIN PALJAK (PIN1)': 
martin@remotehost:~$ 
```

Listing public keys in the authorized_keys file format looks like this:

```bash
$ ssh-keygen -D /usr/lib/opensc-pkcs11.so -e
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQ...
```

## Interim "stable" X509/PKCS#11 solution

Alon Bar-Lev has a patch against OpenSSH which implements both X509 and PKCS#11 support. The patch was [not accepted to OpenSSH](https://bugzilla.mindrot.org/show_bug.cgi?id=1371) but is available from [Alon Bar-Lev's openssh-pkcs11 page](http://sites.google.com/site/alonbarlev/openssh-pkcs11). The patch depends on `pkcs11-helper` (also by Alon Bar-Lev) and needs to be activated on compile time with `--with-pkcs11`.

Basic usage looks like this:

```bash
$ ssh -# /usr/lib/opensc-pkcs11.so martin@remotehost
Please enter PIN for token 'MARTIN PALJAK (PIN1)': 
martin@remotehost:~$ 
```

## Oldest solution (deprecated)

Portable OpenSSH versions up to version 5.4p1 used to link against libopensc directly. OpenSSH needs to be compiled with `--with-opensc` (not done by most Linux distros) and the implementation has issues. Recent snapshot versions of OpenSSH don't include the relevant source code any more. Recent snapshot versions of OpenSC don't encourage/support linking directly against libopensc as well.
