If you want to use OpenSC with Cygwin OpenSSH utilities, such as ssh-agent or ssh,
then OpenSC has to be compiled for Cygwin. To do this follow these steps:

###  Prepare for a fresh Cygwin install
When building OpenSC we're going to be running the reconfiguration step of the OpenSC build process.
One side effect is that this step may try to incorporate additional features that are detected in 
your current Cygwin installation, which can complicate the package dependencies.
So these instructions are based on starting from a fresh Cygwin installation. Thus:
1. Remove all versions of bash, gcc, peagent, git, etc.
2. Clear PATH var of any ref to other make or gcc utils etc.
3. Temporarily unset CYGWIN environment variable while building and installing. 
   Currently having CYGWIN set causes make install to fail in the install-exec-hook stage.

### Install Cygwin base 
1. Go to https://cygwin.com/install.html.
2. Run setup-x86_64.exe & save it for running later.
3. Install to c:\cygwin64.
4. Select All Users.
5. Save packages to a convenient location, e.g. D:\cygwin-packages.
6. Select mirror etc.

### Install additional Cygwin packages
By default the base packages will be ready to be downloaded and installed,
but before exiting setup, also select the following extra packages:
1. Category/Devel/autoconf: Wrapper scripts for autoconf commands.
2. Category/Devel/autoconf2.5: Current version of the automatic config script builder.
3. Category/Devel/automake: Wrapper for multiple versions of Automake.
4. Category/Devel/automake1.15: a tool for generating GNU compliant Makefiles.
5. Category/Devel/gcc-core: GNU Compiler Collection (C, OpenMP).
6. Category/Devel/gcc-g++: GNU Compiler Collection (C++).
7. Category/Devel/git: Distributed version control system.
8. Category/Devel/libtool: Generic lib support script.
9. Category/Devel/make: The GNU version of the 'make' utility.
10. Category/Devel/pkg-config: Package compiling configuration utility.
11. Category/Libs/libreadline-devel: GNU readline and history libraries.
12. Category/Libs/zlib-devel: gzip de/compression library.
13. Category/Net/libssl-devel or openssl-devel: A general purpose cryptography toolkit...
14. Category/System/procps-ng: System and process monitoring utils - such as pkill. [Optional]

Finish off and have the Cygwin GUI install all these + create desktop icon for Cygwin64 Terminal.

Note: installing the above also causes the following to be installed:
* Category/Net/openssh: The OpenSSH server and client programs.
* Category/Libs/libopenssl100: A general purpose crypto toolkit.
* Plus others.

### Build OpenSC
1. From within a Cygwin terminal, get source for OpenSC via:
<pre><code>$ cd /usr/src
$ git clone https://github.com/OpenSC/OpenSC.git
$ cd OpenSC</code></pre>
3. Then perform commands:
<pre><code>$ ./bootstrap
$ ./configure
$ make
$ make install</code></pre>

### Test access to smart card, e.g. Yubikey
<pre><code>$ pkcs11-tool --test</code></pre>
Or list objects:
<pre><code>$ pkcs11-tool -O</code></pre>

### Add identities from smart card to ssh-agent
1. Start ssh-agent via:
<pre><code>$ eval $(ssh-agent -s)</code></pre>
2. Check current identities, should be none:
<pre><code>$ ssh-add -L</code></pre>
3. Now add identities from smart card:
<pre><code>$ ssh-add -s /usr/local/lib/pkcs11/opensc-pkcs11.dll</code></pre>
4. Check identities were added, this should list a bunch of keys depending what's on the smart card:
<pre><code>$ ssh-add -L</code></pre>
