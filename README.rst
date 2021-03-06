What is BorgBackup?
-------------------
BorgBackup (short: Borg) is a deduplicating backup program.
Optionally, it supports compression and authenticated encryption.

The main goal of Borg is to provide an efficient and secure way to backup data.
The data deduplication technique used makes Borg suitable for daily backups
since only changes are stored.
The authenticated encryption technique makes it suitable for backups to not
fully trusted targets.

See the `installation manual`_ or, if you have already
downloaded Borg, ``docs/installation.rst`` to get started with Borg.

.. _installation manual: http://borgbackup.github.io/borgbackup/installation.html

Main features
~~~~~~~~~~~~~
**Space efficient storage**
  Deduplication based on content-defined chunking is used to reduce the number
  of bytes stored: each file is split into a number of variable length chunks
  and only chunks that have never been seen before are added to the repository.

  To deduplicate, all the chunks in the same repository are considered, no
  matter whether they come from different machines, from previous backups,
  from the same backup or even from the same single file.

  Compared to other deduplication approaches, this method does NOT depend on:

  * file/directory names staying the same

    So you can move your stuff around without killing the deduplication,
    even between machines sharing a repo.

  * complete files or time stamps staying the same

    If a big file changes a little, only a few new chunks will be stored -
    this is great for VMs or raw disks.

  * the absolute position of a data chunk inside a file

    Stuff may get shifted and will still be found by the deduplication
    algorithm.

**Speed**
  * performance critical code (chunking, compression, encryption) is
    implemented in C/Cython
  * local caching of files/chunks index data
  * quick detection of unmodified files

**Data encryption**
    All data can be protected using 256-bit AES encryption, data integrity and
    authenticity is verified using HMAC-SHA256.

**Compression**
    All data can be compressed by lz4 (super fast, low compression), zlib
    (medium speed and compression) or lzma (low speed, high compression).

**Off-site backups**
    Borg can store data on any remote host accessible over SSH.  If Borg is
    installed on the remote host, big performance gains can be achieved
    compared to using a network filesystem (sshfs, nfs, ...).

**Backups mountable as filesystems**
    Backup archives are mountable as userspace filesystems for easy interactive
    backup examination and restores (e.g. by using a regular file manager).

**Easy installation on multiple platforms**
    We offer single-file binaries
    that does not require installing anything - you can just run it on
    the supported platforms:

    * Linux
    * Mac OS X
    * FreeBSD
    * OpenBSD and NetBSD (no xattrs/ACLs support or binaries yet)
    * Cygwin (not supported, no binaries yet)

**Free and Open Source Software**
  * security and functionality can be audited independently
  * licensed under the BSD (3-clause) license


Easy to use
~~~~~~~~~~~
Initialize a new backup repository and create a backup archive::

    $ borg init /mnt/backup
    $ borg create /mnt/backup::Monday ~/Documents

Now doing another backup, just to show off the great deduplication::

    $ borg create --stats /mnt/backup::Tuesday ~/Documents

    Archive name: Tuesday
    Archive fingerprint: 387a5e3f9b0e792e91c...
    Start time: Tue Mar 25 12:00:10 2014
    End time:   Tue Mar 25 12:00:10 2014
    Duration: 0.08 seconds
    Number of files: 358
                      Original size    Compressed size    Deduplicated size
    This archive:          57.16 MB           46.78 MB            151.67 kB  <--- !
    All archives:         114.02 MB           93.46 MB             44.81 MB

For a graphical frontend refer to our complementary project
`BorgWeb <https://github.com/borgbackup/borgweb>`_.


Notes
-----

Borg is a fork of `Attic <https://github.com/jborg/attic>`_ and maintained by
"`The Borg collective`_".

.. _The Borg collective: http://borgbackup.github.io/borgbackup/authors.html

Read `issue #1 <https://github.com/borgbackup/borg/issues/1>`_ about the initial
considerations regarding project goals and policy of the Borg project.

BORG IS NOT COMPATIBLE WITH ORIGINAL ATTIC.
EXPECT THAT WE WILL BREAK COMPATIBILITY REPEATEDLY WHEN MAJOR RELEASE NUMBER
CHANGES (like when going from 0.x.y to 1.0.0). Please read the
`changelog`_ (or ``CHANGES.rst`` in the source distribution) for more
information.

.. _changelog: https://borgbackup.github.io/borgbackup/changes.html

NOT RELEASED DEVELOPMENT VERSIONS HAVE UNKNOWN COMPATIBILITY PROPERTIES.

THIS IS SOFTWARE IN DEVELOPMENT, DECIDE YOURSELF WHETHER IT FITS YOUR NEEDS.

Borg is distributed under a 3-clause BSD license, see `the license`_
for the complete license.

.. _the license: https://borgbackup.github.io/borgbackup/authors.html#license

|build| |coverage|

.. |build| image:: https://travis-ci.org/borgbackup/borg.svg
        :alt: Build Status
        :target: https://travis-ci.org/borgbackup/borg

.. |coverage| image:: http://codecov.io/github/borgbackup/borg/coverage.svg?branch=master
        :alt: Test Coverage
        :target: http://codecov.io/github/borgbackup/borg?branch=master
