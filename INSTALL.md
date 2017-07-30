Installation
============

On Debian, Ubuntu and derivatives
---------------------------------

Run `apt-get install backupninja`.

By hand
-------

Requirements:

        bash gawk

Recommended:

        rdiff-backup duplicity borgbackup rsync gzip hwinfo sfdisk cryptsetup flashrom hwinfo

To install backupninja, simply do the following:

        $ ./autogen.sh
        $ ./configure
        $ make
        $ make install

You may wish to change the install locations, or other options. To find
the available possibilities, run `./configure --help`.
