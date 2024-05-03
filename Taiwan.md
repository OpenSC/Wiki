# Taiwan

Giesecke & Devrient tells us Taiwan is using StarSign based tokens for a nation-wide PKI project. As StarSign is afaik a PKCS15 compliant profile it should be supported in OpenSC. However due to a bug in an older version of the StarSign software
used for at least for some tokens the profile on these tokens are not PKCS15 compliant and hence these
smartcards are currently not supported in OpenSC.

To implement a workaround for these tokens shouldn't be too difficult, however due to a lack of test tokens
it hasn't been implemented yet. If you have one of these tokens and are interested in OpenSC support for it
create issue/PR/discussion on GitHub.
