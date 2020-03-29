+++
title = "Working with kernel/initrd in 2020"
summary = "Linux systems are made up of kernels and filesystems.  As part of the boot process, most sysetms load an initial subset of tools into RAM as part of detecting and activating hardware through an initrd.  This process remains mostly unchanged for the last fifteen years, but modern tooling makes it very relevant in 2020"

date = 2020-03-15
lastmod = 2020-03-15
draft = true

tags = ["linux", "hardware"]
+++

## Notes

The initrd used to be a compressed filesystem.  Now, it appears to be a `cpio` archive.  I wonder when that changed.  It's not that surprising in retrospect.  The venerable file format joined the world in Unix V7 and has been at the core of the RPM standard from the very beginning.  It's like `tar`, but in addition to storing files, it also stores metadata in the archive before the file data.  [cpio Wikipedia Page](https://en.wikipedia.org/wiki/Cpio)

Let's figure out what's in the initrd for debian buster.

### Download and unpack your initrd

```bash
curl -LO http://ftp.us.debian.org/debian/dists/buster/main/installer-amd64/current/images/netboot/debian-installer/amd64/initrd.gz

gunzip initrd.gz

mkdir initrdmount

cd initrdmount
cpio -i < ../initrd

```

The original file is about thirty megabytes which uncompresses to a still meager 94 megabytes.  That's small enough to load uncompressed in RAM and still have plenty of room, even on relatively small servers.  It works fine for me on an old system with only 512M of RAM.

### What's inside?

According to find, there are 2085 files in that little archive, but that's a bit deceptive.  Let's review some of the larger groups of files we can safely ignore in our exploration:

There are 782 files in `/lib/modules` because the debian installer needs to detect so many things and load modules for them.  Those modules need configuration files too.  The alsa subsystem needs 81 soundcard config files in the initrd to do discovery.

Handling braille terminal support for different readers and keyboard configurations takes 399 files.

Getting all the certs bootstrapped so the initrd can safely talk to the internet requires 258 files.

106 of the files in `/bin` and `/sbin/` are simply symbolic links to the `busybox` binary.

| count | type  | comment |
|---|---|---|
| 782 | modules |  |
| 399 | braille terminal types | [Brltty](http://mielke.cc/brltty/) |
| 258 | certificates |  |
| 106 | busybox symlinks | [Busybox](https://busybox.net/) is a single binary that can act like ~115 different linux commands |





