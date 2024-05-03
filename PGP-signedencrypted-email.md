# PGP signed/encrypted email

Most people use the [GnuPG](http://www.gnupg.org/) program to do that.
The experimental development version gnupg 1.9 used to include support
for using smart cards using opensc. But since the code has changed and
now GnuPG has its own smart card code.

In practice many cards that work well with OpenSC should also work well
with GnuPG. If that is not the case, you need to contact GnuPG developers,
as their code is completely independent.
