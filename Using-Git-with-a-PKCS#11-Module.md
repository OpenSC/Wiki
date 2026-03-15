# Using Git with a PKCS#11 Module

Signing your Git commits with a hardware security module (HSM) or a smart card via **PKCS#11** adds a robust layer of security by ensuring your private keys never leave the physical device. This guide covers how to use a hardware security module (HSM) via PKCS#11 to both sign your commits and authenticate your git push/pull operations using the SSH protocol.


## 1. Prerequisites

Before starting, ensure you have the following installed and configured:

* **Git** (version 2.34.0 or higher is recommended).
* **OpenSC** or a specific PKCS#11 module provider for your hardware.
* A hardware device (YubiKey, Smartcard, etc.) with an existing keypair.


## 2. Identify Your Provider Path

You need the absolute path to your PKCS#11 shared library. Common locations include:

* **macOS:** `/usr/local/lib/opensc-pkcs11.so`
* **Linux:** `/usr/lib/x86_64-linux-gnu/opensc-pkcs11.so`
* **Windows:** `C:\Program Files\OpenSC Project\OpenSC\pkcs11\opensc-pkcs11.dll`


## 3. Configuration: Using SSH Keys (Recommended)

Modern Git allows signing via SSH keys, which is generally more straightforward to bridge with PKCS#11 than GPG.

### Step 1: Export the Public Key

Find the public key associated with your hardware token:

```bash
ssh-keygen -D "/path/to/opensc-pkcs11.so"
```

Copy the output (e.g. lines starting with `ssh-rsa` or `ecdsa-sha2-nistp256`) and save it to a file, e.g., `~/.ssh/id_token.pub`. This output only contains your public key, it does *not* include a certificate that may be connected with that public key.

### Step 2: Configure Git

Run the following commands to tell Git to use SSH for signing:

```bash
# Set the signing format to SSH
git config --global gpg.format ssh

# Point Git to your public key
git config --global gpg.ssh.allowedSignersFile "~/.ssh/id_token.pub"

# Tell SSH which PKCS11 module to use for the operation (optional if you use gpg-agent)
git config --global core.sshCommand "ssh -I /path/to/opensc-pkcs11.so"

# Enable automatic signing for all commits (optional)
git config --global commit.gpgsign true
```

#### SSH with PKCS#11 Option A: Enable PKCS#11 globally

You can configure this in your ~/.ssh/config file so it works for all SSH traffic (e.g. to GitHub), not just Git:
```
Host *
    PKCS11Provider /path/to/opensc-pkcs11.so
    IdentitiesOnly  yes
    IdentityFile    ~/.ssh/id_bdr.pub
```

## 4. Registering the Signing Key on GitHub

To ensure GitHub recognizes your PKCS#11-backed signatures as **Verified**, you must upload the public key to your account.

### Step 1: Retrieve Your Public Key

Display the key you exported earlier:

```bash
cat ~/.ssh/id_token.pub
```

### Step 2: Add the Key to GitHub

1. Log in to **GitHub** and navigate to **Settings** > **SSH and GPG keys**.

#### Add Signing Key

2. Click the **New SSH key** button.
3. **Title:** Enter a descriptive name (e.g., "Hardware Token - PKCS11").
4. **Key type:** Select **Signing Key** from the dropdown menu.
5. **Key:** Paste your public key string here.
6. Click **Add SSH key**.

#### Add Authentication Key

7. Click the **New SSH key** button.
8. **Title:** Enter a descriptive name (e.g., "Hardware Token - PKCS11").
9. **Key type:** Select **Authentication Key** from the dropdown menu.
10. **Key:** Paste your public key string here.
11. Click **Add SSH key**.

### Step 3: Enable Vigilant Mode (Optional)

On the same **SSH and GPG keys** page, scroll down to **Vigilant mode** and check **Flag unsigned commits as unverified**. This helps you quickly spot any commits that weren't signed by your hardware token.

## 5. Verification and Usage

To create a signed commit (if you haven't enabled global signing):

```bash
git commit -S -m "My signed commit"
git push
```