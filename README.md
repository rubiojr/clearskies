ClearSkies
==========

ClearSkies is a sync program similar to DropBox, except it does not require a
monthly fee.  Instead, you set up shares between two or more computers and the
sharing happens amongst them directly.

ClearSkies is inspired by BitTorrent Sync, but it has an open protocol that can
be audited for security.

The protocol is layered in such a way that other applications can take advantage
of it for purposes other than file sync.

This repository contains the protocol documentation as well as an in-the-works
proof-of-concept implementation.  The proof-of-concept implementation is open
source and free software, under the GPLv3 (see the LICENSE file for details.)


The Protocol
------------

The ClearSkies protocol has been documented and is in a draft state.  It can be
found in the `protocol/` directory.
[protocol/core.md](https://github.com/jewel/clearskies/blob/master/protocol/core.md)
is a good starting place.

The protocol features:

* Simple-to-share access codes
* Read-write sync
* Read-only sharing
* Encrypted connections
* Shallow copy (do not sync certain files from peer)
* Subtree copy (only sync certain directories from peer)
* Streaming support
* Rsync file transfer (extension)
* Gzip compression (extension)
* Encrypted backup sharing to an untrusted peer (future extension)
* Media streaming (future extension)
* Photo thumbnails (future extension)

The protocol is designed to be a common base for other sync programs, so that
they can interoperate with each other.  For example, a hypothetical
wifi-enabled MIDI piano could speak the protocol and thereby sync its saved
files to the owner's computer or tablet, without the piano manufacturer needing
to write any PC or tablet software.


The Software
------------

The software in this repository is a proof-of-concept of the protocol, written
in ruby.  It consists of a background daemon and a command-line interface to
control that daemon.  The proof-of-concept is currently out of date in 

We are focusing our effort on porting the daemon to C++ in a [different
repository](https://github.com/larroy/clearskies_core) which will replace the
ruby proof-of-concept once it's ready.  The C++ library should be portable to
a wide variety of operating systems, including mobile.

The C++ daemon is being ported to android in [this
repository](https://github.com/cachapa/clearskies_core_android).

There is a separate effort to get the ruby proof-of-concept to run under jruby
on android in [this
repository](https://github.com/onionjake/clearskies-ruboto).


Status
------

The software is currently barely functional, in read-write mode only.  It is
not yet ready for production use.  IT MAY EAT YOUR DATA.  Only use it on test
data or on data that you have backed up someplace safe.


Security
--------

The software does not attempt to provide anonymity.  Access code sharing is
designed to reduce the impact of surveillance by using one-time codes by
default, and using perfect forward secrecy on the wire.

Setup of a share is vulnerable to an active man-in-the-middle attack if the
channel used to send the access code is insecure.

For example, if Bob sends Alice an access code over SMS, Eve can try to connect
to Bob before Alice does.  Alice will not be able to connect to the share.  Eve
can even create another share and issue the same access code to fool Alice into
thinking she has connected to Bob.

It is believed that security-conscious users will automatically avoid this
problem by sharing the access codes over secure channels.


Installation
------------

It is currently only tested on Linux.  (It should also work on ruby 1.9 on OS X
and Windows, if not please file an issue.)

If you already have a working ruby 1.9 or 2.0:

```bash
gem install rb-inotify ffi
```

Otherwise, installing dependencies on Ubuntu or Debian:

```bash
apt-get install libgnutls26 ruby1.9.1 ruby-rb-inotify ruby-ffi
```

Note: The version of "ffi" in the Debian stable (wheezy) apt repository has
issues.  The version of "rb-inotify" in Ubuntu 12.04 (precise) also has issues.
In those cases, install the gems via ruby gems:

```bash
apt-get remove ruby-rb-inotify ruby-ffi
apt-get install ruby-dev
gem install rb-inotify ffi
```

Clone this repo:

```bash
git clone https://github.com/jewel/clearskies
```

To start and share a directory:

```bash
cd clearskies
./clearskies start # add --no-fork to run in foreground
./clearskies share ~/important-stuff --mode=read-write
```


This will print out a "SYNC" code.  Copy the code to the other computer, and
then add the share to begin syncing:

```bash
./clearskies attach $CODE ~/important-stuff
```


Contributing
------------

If you are a professional cryptographer with interest in this project, any
feedback on the protocol is very welcome.

A major area that needs work is creating GUIs for each platform, such as GTK,
Cocoa, QT, Android, iOS, browser-based, and a Windows program.  GUIs do not
need to be written in any particular language, since they can control the
daemon using a simple JSON protocol, which is documented in
`protocol/control.md`.  This repository will only contain the command-line user
interface, but will happily link to any GUIs that exist.

Issues and pull requests are welcome.

The project mailing list is on [google
groups](https://groups.google.com/group/clearskies-dev).  (It is possible to
participate via email if you do not have a google account.)
