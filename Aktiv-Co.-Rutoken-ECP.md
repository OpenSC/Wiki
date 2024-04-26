# Aktiv Co. Rutoken ECP

[Aktiv Co.](http://www.aktiv-company.ru/) offers the [Rutoken ECP](https://www.rutoken.ru/products/all/rutoken-ecp/), an USB crypto token with 64K memory and support for RSA keys up to 2048bit key length.

## Rutoken ECP

* **USB IDs:** 0a89:0030
* **Memory:** 64K
* **ATRs:**
  * [Aktiv Rutoken ECP](https://smartcard-atr.apdu.fr/parse?ATR=3B8B015275746F6B656E20445320C1): `3B 8B 01 52 75 74 6F 6B 65 6E 20 44 53 20 C1`
  * [Rutoken ECP](https://smartcard-atr.apdu.fr/parse?ATR=3B8B015275746F6B656E20454350A0): `3B 8B 01 52 75 74 6F 6B 65 6E 20 45 43 50 A0`

## On-board cryptographic functions

* RSA (with RSA keys up to 2048 bits)
* GOST R 34.10-2001 ([RFC 5832](http://tools.ietf.org/html/rfc5832))
* [GOST 34.11-94](http://en.wikipedia.org/wiki/GOST_(hash_function)) ([RFC 5831](http://tools.ietf.org/html/rfc5831))
* [GOST 28147-89](http://en.wikipedia.org/wiki/GOST_(block_cipher)) ([RFC 5830](http://tools.ietf.org/search/rfc5830))
* Key generation: ElGamal and Diffie-Hellman schemes

## Authentication

* 3 categories of owners: Administrator, User, Guest
* 2 Global PIN-codes: Administrator and User
* Local PIN-codes
* Combined authentication
* The possibility of simultaneous control of the access rights by the 7 Local PIN-codes

## File system features

* File structure of ISO/IEC 7816-4
* The level of subdirectory - limited by space available for file system
* Number of file objects inside directory - up to 255, inclusive
* Using files Rutoken Special File (RSF-files) to store keys and PIN-codes
* Storage of private and symmetric keys, without the possibility of exports from device
* Predefined directory for storing different kinds of key information (RSF-files) and automatic selection of the predefined directories
* The total amount of memory for file structure - 64 kB

## Examples of usage

### Initialize

```bash
$ pkcs15-init --erase-card -p rutoken_ecp
$ pkcs15-init --create-pkcs15 --so-pin "87654321" --so-puk ""
$ pkcs15-init --store-pin --label "User PIN" --auth-id 02 --pin "12345678" --puk "" --so-pin "87654321" --finalize
$ pkcs15-tool -D
Using reader with a card: Aktiv Rutoken ECP 00 00
PKCS#15 Card [Rutoken ECP]:
	Version        : 0
	Serial number  : 000000002CDB7256
	Manufacturer ID: Aktiv Co.
	Last update    : 20121114083252Z
	Flags          : EID compliant

PIN [Security Officer PIN]
	Object Flags   : [0x3], private, modifiable
	ID             : 01
	Flags          : [0x99], case-sensitive, unblock-disabled, initialized, soPin
	Length         : min_len:8, max_len:32, stored_len:32
	Pad char       : 0x00
	Reference      : 1
	Type           : ascii-numeric

PIN [User PIN]
	Object Flags   : [0x3], private, modifiable
	ID             : 02
	Flags          : [0x19], case-sensitive, unblock-disabled, initialized
	Length         : min_len:4, max_len:32, stored_len:32
	Pad char       : 0x00
	Reference      : 2
	Type           : ascii-numeric
```

### Generate private key

```bash
$ pkcs15-init -G rsa/1024 --auth-id 02 --label "My Private Key" --public-key-label "My Public Key"
Using reader with a card: Aktiv Rutoken ECP 00 00
User PIN [User PIN] required.
Please enter User PIN [User PIN]: 
$ pkcs15-tool --list-keys
Using reader with a card: Aktiv Rutoken ECP 00 00
Private RSA Key [My Private Key]
	Object Flags   : [0x3], private, modifiable
	Usage          : [0x4], sign
	Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
	ModLength      : 1024
	Key ref        : 1 (0x1)
	Native         : yes
	Path           : 3f001000100060020001
	Auth ID        : 02
	ID             : 04830838b7c9752bd0e90c96a88b989a80f45181
	GUID           : {04830838-b7c9-752b-d0e9-0c96a88b989a}
```

### Generate openssl certificate request

```bash
$ pkcs15-tool --list-keys
Using reader with a card: Aktiv Rutoken ECP 00 00
Private RSA Key [My Private Key]
	Object Flags   : [0x3], private, modifiable
	Usage          : [0x4], sign
	Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
	ModLength      : 1024
	Key ref        : 1 (0x1)
	Native         : yes
	Path           : 3f001000100060020001
	Auth ID        : 02
	ID             : 04830838b7c9752bd0e90c96a88b989a80f45181
	GUID           : {04830838-b7c9-752b-d0e9-0c96a88b989a}
$ openssl
OpenSSL> engine dynamic -pre SO_PATH:/usr/lib/openssl/engines/engine_pkcs11.so -pre ID:pkcs11 -pre LIST_ADD:1 -pre LOAD -pre MODULE_PATH:opensc-pkcs11.so
(dynamic) Dynamic engine loading support
[Success]: SO_PATH:/usr/lib/openssl/engines/engine_pkcs11.so
[Success]: ID:pkcs11
[Success]: LIST_ADD:1
[Success]: LOAD
[Success]: MODULE_PATH:opensc-pkcs11.so
Loaded: (pkcs11) pkcs11 engine
OpenSSL> 
OpenSSL> req -engine pkcs11 -new -key slot_1-id_04830838b7c9752bd0e90c96a88b989a80f45181 -keyform engine -out req.csr -subj "/C=RU/ST=Moscow/L=Moscow/O=KORUS/OU=IT/CN=Sergey Safarov/emailAddress=s.safarov@mail.com"
engine "pkcs11" set.
PKCS#11 token PIN: 
OpenSSL> quit
$ cat req.csr 
-----BEGIN CERTIFICATE REQUEST-----
MIIByTCCATICAQAwgYgxCzAJBgNVBAYTAlJVMQ8wDQYDVQQIDAZNb3Njb3cxDzAN
BgNVBAcMBk1vc2NvdzEOMAwGA1UECgwFS09SVVMxCzAJBgNVBAsMAklUMRcwFQYD
VQQDDA5TZXJnZXkgU2FmYXJvdjEhMB8GCSqGSIb3DQEJARYScy5zYWZhcm92QG1h
aWwuY29tMIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCpY0LM0eBDKATrR47f
oFnzVrMVEl+oeLx8ffAUDRIsKyILBDEOrhj3wD/8qNSBXl0CTXRL1jPJ/Cmjy6si
+AJtVCbnuhe2LP064kcbj17eurAuAhPwKMlLhjchsPoEcjN22fQYAie+XN+atTtW
auFanOV6valVGsyboScbPyDbeQIDAQABoAAwDQYJKoZIhvcNAQEFBQADgYEAE0gR
T3KHARExb0aFBEl9x6+oPPQSJfE/qh5S0vc7d68JEnjSwkx4Zdy9CQW+ympHpRde
t5Dn68Xs3OjXUOJ6rEsAQcgya3PVcgjljY46555bFBz5V9LIOh+qTREGKpvOMTdO
XUFiwTApskr8pn8Gc2mFV1cA+dEf3S9XNluNVKA=
-----END CERTIFICATE REQUEST-----
```

If you have errors when loading engine - check the location of directory of engine_pkcs11.so
(for example: `/usr/lib/engines/engine_pkcs11.so`) and use it further when loading engine.

### Generate self-signed certificate

```bash
$ pkcs15-tool --list-keys
Using reader with a card: Aktiv Rutoken ECP 00 00
Private RSA Key [My Private Key]
	Object Flags   : [0x3], private, modifiable
	Usage          : [0x4], sign
	Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
	ModLength      : 1024
	Key ref        : 1 (0x1)
	Native         : yes
	Path           : 3f001000100060020001
	Auth ID        : 02
	ID             : 04830838b7c9752bd0e90c96a88b989a80f45181
	GUID           : {04830838-b7c9-752b-d0e9-0c96a88b989a}
$ openssl
OpenSSL> engine dynamic -pre SO_PATH:/usr/lib/openssl/engines/engine_pkcs11.so -pre ID:pkcs11 -pre LIST_ADD:1 -pre LOAD -pre MODULE_PATH:opensc-pkcs11.so
(dynamic) Dynamic engine loading support
[Success]: SO_PATH:/usr/lib/openssl/engines/engine_pkcs11.so
[Success]: ID:pkcs11
[Success]: LIST_ADD:1
[Success]: LOAD
[Success]: MODULE_PATH:opensc-pkcs11.so
Loaded: (pkcs11) pkcs11 engine
OpenSSL> req -engine pkcs11 -x509 -new -key slot_1-id_04830838b7c9752bd0e90c96a88b989a80f45181  -keyform engine -out ca.crt -subj "/C=RU/ST=Moscow/L=Moscow/O=KORUS/OU=IT/CN=Sergey Safarov/emailAddress=s.safarov@mail.com"
engine "pkcs11" set.
PKCS#11 token PIN: 
OpenSSL> quit
$ cat ca.crt
-----BEGIN CERTIFICATE-----
MIICiTCCAfICCQCPMHdgV/rQBjANBgkqhkiG9w0BAQUFADCBiDELMAkGA1UEBhMC
UlUxDzANBgNVBAgMBk1vc2NvdzEPMA0GA1UEBwwGTW9zY293MQ4wDAYDVQQKDAVL
T1JVUzELMAkGA1UECwwCSVQxFzAVBgNVBAMMDlNlcmdleSBTYWZhcm92MSEwHwYJ
KoZIhvcNAQkBFhJzLnNhZmFyb3ZAbWFpbC5jb20wHhcNMTIxMTE0MDg1MTMyWhcN
MTIxMjE0MDg1MTMyWjCBiDELMAkGA1UEBhMCUlUxDzANBgNVBAgMBk1vc2NvdzEP
MA0GA1UEBwwGTW9zY293MQ4wDAYDVQQKDAVLT1JVUzELMAkGA1UECwwCSVQxFzAV
BgNVBAMMDlNlcmdleSBTYWZhcm92MSEwHwYJKoZIhvcNAQkBFhJzLnNhZmFyb3ZA
bWFpbC5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAKljQszR4EMoBOtH
jt+gWfNWsxUSX6h4vHx98BQNEiwrIgsEMQ6uGPfAP/yo1IFeXQJNdEvWM8n8KaPL
qyL4Am1UJue6F7Ys/TriRxuPXt66sC4CE/AoyUuGNyGw+gRyM3bZ9BgCJ75c35q1
O1Zq4Vqc5Xq9qVUazJuhJxs/INt5AgMBAAEwDQYJKoZIhvcNAQEFBQADgYEAlmet
AeeNkdUjCiP3nk8PJ5lW8d+ohl55W6gsi4pRvLwXH/CsKzU3scNbPKnEQz1FGpfZ
xHp+LB1jZSUci1r6saQKkDFLXLQqIedRhR2Bevx5msw+ydM3GwwRW9K0IumwrYwp
9IGRsBOT1s7eTZfYNURmqdhP5hIdgo3dJ4utr1c=
-----END CERTIFICATE-----
```

### Sign certificate

```bash
$ pkcs15-tool --list-keys
Using reader with a card: Aktiv Rutoken ECP 00 00
Private RSA Key [My Private Key]
	Object Flags   : [0x3], private, modifiable
	Usage          : [0x4], sign
	Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
	ModLength      : 1024
	Key ref        : 1 (0x1)
	Native         : yes
	Path           : 3f001000100060020001
	Auth ID        : 02
	ID             : 04830838b7c9752bd0e90c96a88b989a80f45181
	GUID           : {04830838-b7c9-752b-d0e9-0c96a88b989a}
$ openssl
OpenSSL> engine dynamic -pre SO_PATH:/usr/lib/openssl/engines/engine_pkcs11.so -pre ID:pkcs11 -pre LIST_ADD:1 -pre LOAD -pre MODULE_PATH:opensc-pkcs11.so
(dynamic) Dynamic engine loading support
[Success]: SO_PATH:/usr/lib/openssl/engines/engine_pkcs11.so
[Success]: ID:pkcs11
[Success]: LIST_ADD:1
[Success]: LOAD
[Success]: MODULE_PATH:opensc-pkcs11.so
Loaded: (pkcs11) pkcs11 engine
OpenSSL> ca -engine pkcs11 -keyfile slot_1-id_04830838b7c9752bd0e90c96a88b989a80f45181  -keyform engine -cert ca.crt -in req.csr -out tester.crt
Using configuration from /etc/pki/tls/openssl.cnf
engine "pkcs11" set.
PKCS#11 token PIN: 
Check that the request matches the signature
Signature ok
Certificate Details:
        Serial Number: 1 (0x1)
        Validity
            Not Before: Nov 14 09:04:44 2012 GMT
            Not After : Nov 14 09:04:44 2013 GMT
        Subject:
            countryName               = RU
            stateOrProvinceName       = Moscow
            organizationName          = KORUS
            organizationalUnitName    = IT
            commonName                = Sergey Safarov
            emailAddress              = s.safarov@mail.com
        X509v3 extensions:
            X509v3 Basic Constraints: 
                CA:FALSE
            Netscape Comment: 
                OpenSSL Generated Certificate
            X509v3 Subject Key Identifier: 
                DD:47:C4:DB:57:4B:04:BB:67:82:5A:88:DF:93:1C:6D:E2:CB:58:0F
            X509v3 Authority Key Identifier: 
                DirName:/C=RU/ST=Moscow/L=Moscow/O=KORUS/OU=IT/CN=Sergey Safarov/emailAddress=s.safarov@mail.com
                serial:8F:30:77:60:57:FA:D0:06

Certificate is to be certified until Nov 14 09:04:44 2013 GMT (365 days)
Sign the certificate? [y/n]:y


1 out of 1 certificate requests certified, commit? [y/n]y
Write out database with 1 new entries
Data Base Updated
OpenSSL> quit
$ cat tester.crt 
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 1 (0x1)
        Signature Algorithm: sha1WithRSAEncryption
        Issuer: C=RU, ST=Moscow, L=Moscow, O=KORUS, OU=IT, CN=Sergey Safarov/emailAddress=s.safarov@mail.com
        Validity
            Not Before: Nov 14 09:04:44 2012 GMT
            Not After : Nov 14 09:04:44 2013 GMT
        Subject: C=RU, ST=Moscow, O=KORUS, OU=IT, CN=Sergey Safarov/emailAddress=s.safarov@mail.com
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (1024 bit)
                Modulus:
                    00:a9:63:42:cc:d1:e0:43:28:04:eb:47:8e:df:a0:
                    59:f3:56:b3:15:12:5f:a8:78:bc:7c:7d:f0:14:0d:
                    12:2c:2b:22:0b:04:31:0e:ae:18:f7:c0:3f:fc:a8:
                    d4:81:5e:5d:02:4d:74:4b:d6:33:c9:fc:29:a3:cb:
                    ab:22:f8:02:6d:54:26:e7:ba:17:b6:2c:fd:3a:e2:
                    47:1b:8f:5e:de:ba:b0:2e:02:13:f0:28:c9:4b:86:
                    37:21:b0:fa:04:72:33:76:d9:f4:18:02:27:be:5c:
                    df:9a:b5:3b:56:6a:e1:5a:9c:e5:7a:bd:a9:55:1a:
                    cc:9b:a1:27:1b:3f:20:db:79
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Basic Constraints: 
                CA:FALSE
            Netscape Comment: 
                OpenSSL Generated Certificate
            X509v3 Subject Key Identifier: 
                DD:47:C4:DB:57:4B:04:BB:67:82:5A:88:DF:93:1C:6D:E2:CB:58:0F
            X509v3 Authority Key Identifier: 
                DirName:/C=RU/ST=Moscow/L=Moscow/O=KORUS/OU=IT/CN=Sergey Safarov/emailAddress=s.safarov@mail.com
                serial:8F:30:77:60:57:FA:D0:06

    Signature Algorithm: sha1WithRSAEncryption
        14:4b:39:32:81:37:aa:95:8f:db:f8:42:64:21:32:6f:11:ad:
        8e:c1:ef:bf:20:93:e2:6f:66:80:fc:f6:af:ad:5c:80:6f:98:
        1f:28:ea:14:87:f9:6c:5d:59:2c:0a:42:fd:a1:ed:34:f5:4b:
        c5:7c:5e:2f:16:48:27:a1:c5:b8:0d:f3:64:d0:7f:f6:55:fc:
        36:3c:30:bf:7f:e9:62:23:e6:4f:45:76:43:9f:ae:a7:b4:5a:
        ca:e7:98:f3:6e:09:91:58:f3:4b:6f:36:e3:88:fc:48:54:95:
        a7:be:58:b8:97:86:5c:57:7e:cb:c4:0a:f2:d6:ac:c3:90:11:
        2b:00
-----BEGIN CERTIFICATE-----
MIIDfjCCAuegAwIBAgIBATANBgkqhkiG9w0BAQUFADCBiDELMAkGA1UEBhMCUlUx
DzANBgNVBAgMBk1vc2NvdzEPMA0GA1UEBwwGTW9zY293MQ4wDAYDVQQKDAVLT1JV
UzELMAkGA1UECwwCSVQxFzAVBgNVBAMMDlNlcmdleSBTYWZhcm92MSEwHwYJKoZI
hvcNAQkBFhJzLnNhZmFyb3ZAbWFpbC5jb20wHhcNMTIxMTE0MDkwNDQ0WhcNMTMx
MTE0MDkwNDQ0WjB3MQswCQYDVQQGEwJSVTEPMA0GA1UECAwGTW9zY293MQ4wDAYD
VQQKDAVLT1JVUzELMAkGA1UECwwCSVQxFzAVBgNVBAMMDlNlcmdleSBTYWZhcm92
MSEwHwYJKoZIhvcNAQkBFhJzLnNhZmFyb3ZAbWFpbC5jb20wgZ8wDQYJKoZIhvcN
AQEBBQADgY0AMIGJAoGBAKljQszR4EMoBOtHjt+gWfNWsxUSX6h4vHx98BQNEiwr
IgsEMQ6uGPfAP/yo1IFeXQJNdEvWM8n8KaPLqyL4Am1UJue6F7Ys/TriRxuPXt66
sC4CE/AoyUuGNyGw+gRyM3bZ9BgCJ75c35q1O1Zq4Vqc5Xq9qVUazJuhJxs/INt5
AgMBAAGjggEGMIIBAjAJBgNVHRMEAjAAMCwGCWCGSAGG+EIBDQQfFh1PcGVuU1NM
IEdlbmVyYXRlZCBDZXJ0aWZpY2F0ZTAdBgNVHQ4EFgQU3UfE21dLBLtnglqI35Mc
beLLWA8wgacGA1UdIwSBnzCBnKGBjqSBizCBiDELMAkGA1UEBhMCUlUxDzANBgNV
BAgMBk1vc2NvdzEPMA0GA1UEBwwGTW9zY293MQ4wDAYDVQQKDAVLT1JVUzELMAkG
A1UECwwCSVQxFzAVBgNVBAMMDlNlcmdleSBTYWZhcm92MSEwHwYJKoZIhvcNAQkB
FhJzLnNhZmFyb3ZAbWFpbC5jb22CCQCPMHdgV/rQBjANBgkqhkiG9w0BAQUFAAOB
gQAUSzkygTeqlY/b+EJkITJvEa2Owe+/IJPib2aA/PavrVyAb5gfKOoUh/lsXVks
CkL9oe009UvFfF4vFkgnocW4DfNk0H/2Vfw2PDC/f+liI+ZPRXZDn66ntFrK55jz
bgmRWPNLbzbjiPxIVJWnvli4l4ZcV37LxAry1qzDkBErAA==
-----END CERTIFICATE-----
```

### Store certificate

```bash
$ pkcs15-tool --list-keys
Using reader with a card: Aktiv Rutoken ECP 00 00
Private RSA Key [My Private Key]
	Object Flags   : [0x3], private, modifiable
	Usage          : [0x4], sign
	Access Flags   : [0x1D], sensitive, alwaysSensitive, neverExtract, local
	ModLength      : 1024
	Key ref        : 1 (0x1)
	Native         : yes
	Path           : 3f001000100060020001
	Auth ID        : 02
	ID             : 04830838b7c9752bd0e90c96a88b989a80f45181
	GUID           : {04830838-b7c9-752b-d0e9-0c96a88b989a}
$ pkcs15-init --store-certificate tester.crt --auth-id 02 --id 04830838b7c9752bd0e90c96a88b989a80f45181 --format pem
$ pkcs15-tool --read-certificate 04830838b7c9752bd0e90c96a88b989a80f45181
Using reader with a card: Aktiv Rutoken ECP 00 00
-----BEGIN CERTIFICATE-----
MIIDfjCCAuegAwIBAgIBATANBgkqhkiG9w0BAQUFADCBiDELMAkGA1UEBhMCUlUx
DzANBgNVBAgMBk1vc2NvdzEPMA0GA1UEBwwGTW9zY293MQ4wDAYDVQQKDAVLT1JV
UzELMAkGA1UECwwCSVQxFzAVBgNVBAMMDlNlcmdleSBTYWZhcm92MSEwHwYJKoZI
hvcNAQkBFhJzLnNhZmFyb3ZAbWFpbC5jb20wHhcNMTIxMTE0MDkwNDQ0WhcNMTMx
MTE0MDkwNDQ0WjB3MQswCQYDVQQGEwJSVTEPMA0GA1UECAwGTW9zY293MQ4wDAYD
VQQKDAVLT1JVUzELMAkGA1UECwwCSVQxFzAVBgNVBAMMDlNlcmdleSBTYWZhcm92
MSEwHwYJKoZIhvcNAQkBFhJzLnNhZmFyb3ZAbWFpbC5jb20wgZ8wDQYJKoZIhvcN
AQEBBQADgY0AMIGJAoGBAKljQszR4EMoBOtHjt+gWfNWsxUSX6h4vHx98BQNEiwr
IgsEMQ6uGPfAP/yo1IFeXQJNdEvWM8n8KaPLqyL4Am1UJue6F7Ys/TriRxuPXt66
sC4CE/AoyUuGNyGw+gRyM3bZ9BgCJ75c35q1O1Zq4Vqc5Xq9qVUazJuhJxs/INt5
AgMBAAGjggEGMIIBAjAJBgNVHRMEAjAAMCwGCWCGSAGG+EIBDQQfFh1PcGVuU1NM
IEdlbmVyYXRlZCBDZXJ0aWZpY2F0ZTAdBgNVHQ4EFgQU3UfE21dLBLtnglqI35Mc
beLLWA8wgacGA1UdIwSBnzCBnKGBjqSBizCBiDELMAkGA1UEBhMCUlUxDzANBgNV
BAgMBk1vc2NvdzEPMA0GA1UEBwwGTW9zY293MQ4wDAYDVQQKDAVLT1JVUzELMAkG
A1UECwwCSVQxFzAVBgNVBAMMDlNlcmdleSBTYWZhcm92MSEwHwYJKoZIhvcNAQkB
FhJzLnNhZmFyb3ZAbWFpbC5jb22CCQCPMHdgV/rQBjANBgkqhkiG9w0BAQUFAAOB
gQAUSzkygTeqlY/b+EJkITJvEa2Owe+/IJPib2aA/PavrVyAb5gfKOoUh/lsXVks
CkL9oe009UvFfF4vFkgnocW4DfNk0H/2Vfw2PDC/f+liI+ZPRXZDn66ntFrK55jz
bgmRWPNLbzbjiPxIVJWnvli4l4ZcV37LxAry1qzDkBErAA==
-----END CERTIFICATE-----
```

## Notes

* When initializing with `pkcs15-init` tool, a PUK code must not be present (press enter when asked or use `--puk ""`).
* Card can be erased with `pkcs15-init --erase-card` (including all keys) without any authentication.
