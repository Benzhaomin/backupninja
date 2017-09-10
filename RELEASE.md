Upstream
========

* prepare the environment:

        export VERSION=x.y.z

* update `configure.in` and `ChangeLog`

        perl -pi -E \
           "s{^AC_INIT\(\[backupninja\],\[[0-9.]+\],}{AC_INIT([backupninja],[$VERSION],}" \
           configure.in && \
        RELEASE_DATE=$(LC_ALL=C date '+%B %d, %Y') perl -pi -E \
           "s{^version\s+[0-9.]+\s+--\s+UNRELEASED$}{version $VERSION -- $RELEASE_DATE}" \
           ChangeLog

* commit, tag and create the tarball:

        git commit configure.in ChangeLog \
            -m "Releasing backupninja $VERSION" && \
        git clean -fdx && \
        git tag -s "backupninja-$VERSION" \
            -m "Releasing backupninja $VERSION" && \
        ./autogen.sh && \
        ./configure && \
        make dist

* compare the content of the generated tarball with the content of the
  previous one

* move the tarball outside of the Git working copy and clean up:

        mkdir -p ../tarballs && \
        mv backupninja-$VERSION.tar.gz ../tarballs/ && \
        make distclean && \
        git clean -fdx

* Install (extract tarball, `.configure && make && sudo make install`)
  and test.

Debian
======

Prepare a new package:

        git checkout debian && \
        gbp import-orig --upstream-vcs-tag="backupninja-$VERSION" \
            ../tarballs/backupninja-$VERSION.tar.gz && \
        gbp dch --auto && \
        dch -e && \
        export DEBIAN_VERSION=$(dpkg-parsechangelog -SVersion) && \
        git commit debian/changelog \
           -m "Releasing backupninja ($DEBIAN_VERSION) to Debian unstable" && \
        gbp buildpackage

Install the `.deb` and test.

Release
=======

* sign the release and push it to Git:

        gpg --armor --detach-sign \
            ../tarballs/backupninja-$VERSION.tar.gz && \
        git checkout debian && \
        gbp buildpackage --git-tag-only --git-sign-tags && \
        git push --follow-tags origin \
            master:master \
            debian:debian \
            pristine-tar:pristine-tar \
            upstream:upstream

* upload the upstream tarball and detached signature to the GitLab
  milestone page with *Edit* → *Attach a file*
* announce the release on the backupninja mailing-list,
  pointing to the milestone web page
* upload to Debian or ask someone listed in the `Uploaders` control
  field to review and upload

Open the next development cycle
===============================

* `git checkout master`
* Add an empty new section in `ChangeLog`, commit and push.
