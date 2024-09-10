# OpenSC Release Howto

Releasing OpenSC should be simple and streamlined, yet a predictable and easily repeatable process. This page describes releasing OpenSC from Git.

## Generic Principles

* At least one (or more, if needed) pre-releases must be done before the actual release.
* After a release candidate has been published, no more merges to the master branch should happen, only release-critical single commits can be cherry-picked and a new release candidate created.
* Normal development should continue when the final release is done.
* OpenSC _usually_ does not release micro updates for previously released versions
  * Micro updates happen in rare occasions when fixing more critical issues

## Preparing Security Relevant Changes

* Request a CVE in case of security relevant fixes or changes.
  * Use Red Hat product security at `secalert@redhat.com` describing the CVE and ask for CVE allocation. Do NOT use mitre directly as their response times are terrible.
  * Filter OSS-Fuzz for [security relevant issues](https://oss-fuzz.com/testcases?open=no&security=yes) that were fixed for this release
  * Filter Coverity scan for _High_ impact issues that were fixed for this release
* Update the [security advisories](https://github.com/OpenSC/OpenSC/wiki/OpenSC-security-advisories)
* Mention CVE ID in the `NEWS` file

## Release Preparations

### On Github

* Add a upcoming release to wiki page [Smart Card Release Testing](https://github.com/OpenSC/OpenSC/wiki/Smart-Card-Release-Testing)
* Create a tracking issue with proposed changelog for `NEWS`, for example [Towards new release 0.22.0](https://github.com/OpenSC/OpenSC/issues/2247)

### In Version Control (git)

Release (or RC) version must be changed in the following files:

* `NEWS`: Make sure to fix the release date for final release!
* `configure.ac` : change package version major/minor/fix as needed, RCs get the package suffix `-rc`, which is removed for the final release
* `configure.ac` : Update the [LT version number](https://www.gnu.org/software/libtool/manual/html_node/Updating-version-info.html), which is required with changes to, for example, `opensc.h` and `libopensc.exports`.
* `.appveyor.yml`: Update the version on first line
* `README.md`: Update the links to the new release and binaries
* `SECURITY.md`

Optionally, discuss changes to _NEWS_ by opening a [new issue](https://github.com/OpenSC/OpenSC/issues/new) with your suggestions.

## Build and Test Binaries

1. Create release tag
    * _Lightweight_ tag for release candidate
      * Via GitHub when creating release - GitHub will automatically create _*-rcX_ as lightweight tag
      * Locally with git

        ```bash
        git tag 0.20.0
          git push origin 0.20.0
        ```

    * _Annotated_ tag for final release
      * Locally with git

        ```bash
        git tag -a 0.20.0
        git push origin 0.20.0
        ```

2. Prepare build artifacts
    * Wait around 50 minutes (after pushing the tag) to allow build artifacts be placed into the [nightly builds](https://github.com/OpenSC/Nightly)
    * All builds must succeed and must not generate more warnings than the previous build.
    * Copy build artifacts selecting the correct branch using the hash of the release commit, e.g.

      ```bash
      git clone https://github.com/OpenSC/OpenSC --single-branch
      cd OpenSC
      BRANCH=`git log --max-count=1 --date=short --abbrev=8 --pretty=format:"%cd_%h"`
      wget https://github.com/OpenSC/Nightly/archive/${BRANCH}.zip
      unzip ${BRANCH}.zip
      ```

    * Do a separate smoke test for all installers and the tarball, [document your results in the Wiki](https://github.com/OpenSC/OpenSC/wiki/Smart-Card-Release-Testing).

3. Create a [new (draft) release](https://github.com/OpenSC/OpenSC/releases):
    * Describe the release including all changes to NEWS (Markdown)
    * Select appropriate tag (when pushed before) or create new one in GitHub (for lightweight tags only)
      * For final releases, select the existing tag, e.g. _0.20.0_; for release candidates choose a new tag, e.g. _0.20.0-rc1_
    * Upload the build artifacts to the new release
      * release tarball, OSX installer, 2 variants (default, light) of Windows installer for both 64b and 32b + separate debug archives
      * For final releases, download signed Windows installers from Signpath.io instead of unsigned installers from AppVeyor (i.e. Nightly builds):

        1. Navigate to [Signpath's outstanding Signing Requests](https://app.signpath.io/Web/8d2463fe-39bd-4a41-bb72-f008b4b1fe17/SigningRequests)
        2. Select the ones that were issued with the creation of the release branch
        3. Check the signing request's Build data URL to match the related AppVeyor build that was triggered with creation of the release branch
        4. Approve signing and wait for completion of the signing process
        5. Download signed artifact from Signpath.io
        6. Upload signed artifact to Github Release
    * Check:
      * _This is a pre-release_ if only creating a release candidate
      * _Set as latest release_ if creating final release

## Announcement

* Write announcement, short human readable version
  * Short overview of OpenSC
  * Important and visible (breaking) changes in this release (not a copy of `NEWS` file)
  * URL-s for downloads
  * Pointers to full verbose information (list of commits, full changelog, closed bugs)
  * SHA-256 hashes of release artifacts (`openssl sha256` or `sha256sum`)
  * Plans for next release
* Find someone to proofread the announcement.
* Via mail publish the announcement on [opensc-announce@lists.sourceforge.net](https://sourceforge.net/p/opensc/mailman/opensc-announce/) and [opensc-devel@lists.sourceforge.net](https://sourceforge.net/p/opensc/mailman/opensc-devel/) lists.
* In case of security relevant changes, forward the announcement to [oss-security@lists.openwall.com](https://www.openwall.com/lists/oss-security/)
* Update the [Main Wiki page](https://github.com/OpenSC/OpenSC/wiki) with links to new release

## Releasing patch versions of OpenSC

* Happens on exceptional basis only when fixing significant problems.
* Release contains only fixup commits on top of previous minor/major release.
* Discuss whether changes need another round of testing.

### How to do it

1. Create new branch from last release tag

    ```bash
    git checkout -b stable-0.X.[Y+1] tags/0.X.Y
    ```

2. Add commits with fixes
    * Use git `cherry-pick -x` to pick commits from `master` to reference commit hashes
3. Remove suffixes from build names in `appveyor.yml` and `.github/build.sh` script

    ```diff
    diff --git a/.appveyor.yml b/.appveyor.yml
    index 4599100ad2..bca1775ae3 100644
    --- a/.appveyor.yml
    +++ b/.appveyor.yml
    @@ -96,8 +96,7 @@ build_script:
          }
      - bash -c "exec 0</dev/null && if [ \"$APPVEYOR_REPO_BRANCH\" == \"master\" -a -z \"$APPVEYOR_PULL_REQUEST_NUMBER\" ]; then ./bootstrap; fi"
      - bash -c "exec 0</dev/null && if [ \"$APPVEYOR_REPO_BRANCH\" == \"master\" -a -n \"$APPVEYOR_PULL_REQUEST_NUMBER\" ]; then ./bootstrap.ci -s \"-pr$APPVEYOR_PULL_REQUEST_NUMBER\"; fi"
    -  - bash -c "exec 0</dev/null && if [ \"$APPVEYOR_REPO_BRANCH\" != \"master\" -a -z \"$APPVEYOR_PULL_REQUEST_NUMBER\" ]; then ./bootstrap.ci -s \"-$APPVEYOR_REPO_BRANCH\"; fi"
    -  - bash -c "exec 0</dev/null && if [ \"$APPVEYOR_REPO_BRANCH\" != \"master\" -a -n \"$APPVEYOR_PULL_REQUEST_NUMBER\" ]; then ./bootstrap.ci -s \"-$APPVEYOR_REPO_BRANCH-prAPPVEYOR_PULL_REQUEST_NUMBER\"; fi"
    +  - bash -c "exec 0</dev/null && if [ \"$APPVEYOR_REPO_BRANCH\" != \"master\" ]; then ./bootstrap; fi"
      # disable features to speed up the script
      - bash -c "exec 0</dev/null && ./configure --with-cygwin-native --disable-openssl --disable-readline --disable-zlib || cat config.log"
      - bash -c "exec 0</dev/null && rm src/getopt.h"
    diff --git a/.github/build.sh b/.github/build.sh
    index 7770590af1..afb58af450 100755
    --- a/.github/build.sh
    +++ b/.github/build.sh
    @@ -15,11 +15,6 @@ if [ "$GITHUB_EVENT_NAME" == "pull_request" ]; then
      else
        SUFFIX="$GITHUB_BASE_REF-pr$PR_NUMBER"
      fi
    -else
    -	BRANCH=$(echo $GITHUB_REF | awk 'BEGIN { FS = "/" } ; { print $3 }')
    -	if [ "$BRANCH" != "master" ]; then
    -		SUFFIX="$BRANCH"
    -	fi
    fi
    if [ -n "$SUFFIX" ]; then
      ./bootstrap.ci -s "$SUFFIX" </code></pre>
    ```

4. Both in `stable-0.X.[Y+1]` and `master` branch:
    * update version in `.appveyor.yml`,
    * update `NEWS` with changes,
    * update version in `SECURITY.md`,
    * update version and LT revision in `configure.ac`.
5. Push release tag `0.X.[Y+1]` into `stable-0.X.[Y+1]` branch

    ```bash
    git tag -a 0.25.1
    git push origin 0.25.1
    ```

6. Wait for [artifacts to build](#build-and-test-binaries)
7. [Create a new release](#build-and-test-binaries)
8. Write [announcement](#announcement)

## Maintenance tasks

### Clean up old nightly builds repository

* The repository [OpenSC/Nightly](https://github.com/OpenSC/Nightly) is used to hold nightly builds for both MacOS and Windows.
* They are pushed on every master commit so the old builds need to be cleaned up regularly.
* Ideally after the new release is done, any old branch before last release can be removed through the github web UI on [OpenSC/Nightly/branches/stale](https://github.com/OpenSC/Nightly/branches/stale).
* For more info, see the [related issues](https://github.com/OpenSC/Nightly/issues/1)

### Refreshing access tokens

There are two access tokens that are used by CI to push artifacts to the Nightly repository. One from Github
Actions and one from AppVeyor. Their expiration can be checked on the
[organization settings](https://github.com/organizations/OpenSC/settings/personal-access-tokens/active) page.

* When they expire, the new need tokens need to be generated in [user settings](https://github.com/settings/tokens?type=beta).
* Both tokens need _Read_ and _Write_ access to code and _Read_ access to metadata in OpenSC/Nightly repository.
* The Github Actions token needs to be inserted into the [OpenSC repository secrets](https://github.com/OpenSC/OpenSC/settings/secrets/actions).
* The AppVeyor token needs to be encrypted using [AppVeyor website](https://ci.appveyor.com/account/frankmorgner/tools/encrypt) and updated in the `.appveyor.yml` for example like done in [#3230](https://github.com/OpenSC/OpenSC/pull/3230).
