+++
title = "very small linux containers"
summary = "containerization...  are you doing it wrong?"

# Optional image to display on homepage (relative to `static/img/` folder).
image_preview = "scaled/containers.jpg"

date = 2014-09-05
lastmod = 2018-01-13
draft = false

tags = ["programming", "devops", "containers", "docker"]

[header]
image = "headers/header-containers.jpg"
+++

# These are my notes, but you can follow along.

Since we sysadmins climbed from the primordial ooze of mainframes, we've been
advocating for a one-to-one mapping of services to hosts.  That makes things
like security and updates easier.  The problem is that computers are
fantastically fast.  When they're only doing one thing, that generally means
that they spend most of their time sitting around doing nothing.  To take
advantage of that time doing nothing, modern computers run hundreds of programs
at once.  A kernel manages access to resources like memory and networking and
shares them with all the running programs, mostly equally.

## Virtualization versus Containerization

When two programs running on the same computer see the same kernel, but can't see each other, that's containerization.

When two programs running on the same computer see different kernels, and thus can't see each other, that's virtualization.

Virtualization gives you fully separate computers which is great for isolation and security, adds a negligible performance penalty, but also multiplies the number of virtual computers to manage with security patches and configuration changes.

Containerization relies on the underlying kernel to keep some things separate, but allows some shared access.  There's only one real computer to manage.

## A little history

Solaris had [Zones](https://en.wikipedia.org/wiki/Solaris_Containers) in 2004. FreeBSD had [Jails](https://wiki.freebsd.org/Jails) from version 4.0 which was released in 2000.  Linux has had [lxc](https://linuxcontainers.org/) since at least 2006.

The path of virtualization has led us to AWS and fully scalable cloud computing.  Our tools for managing many computers came in very handy for that.

The path of containerization is new and exciting.  We're still learning how to
build good tools and good containers.

An important step in that process is to stop thinking about containers the same
way that we think about virtual hosts.  A container doesn't need a logging
infrastructure or init or shells.  Those can all be shared from another
container on the same host.  So, what does a container need?  <b>Just your
code</b>.  How do we shrink a deployment so that it has nothing but the code we
want to run?

# You can build small images

## Start from small base images
* like this one - [scratch](https://registry.hub.docker.com/_/scratch/)
* improve it - [like this](http://blog.xebia.com/2014/07/04/create-the-smallest-possible-docker-container/)

## build your own with [buildroot](http://buildroot.uclibc.org/)
Buildroot is an awesome cross-compilation toolchain designed for embedded systems.  But, it can be great for containers as well.  It eschews package management by cross compiling everything and then squashing it into one tarball.  [@jpetazzo](https://twitter.com/jpetazzo) has even written a [guide](http://blog.docker.com/2013/06/create-light-weight-docker-containers-buildroot/) for building a functional postgres image that weighs in at 16 Megabytes.  

Pretty slick!

## Make a small image with debootstrap
In the [contrib](https://github.com/docker/docker/tree/master/contrib) directory of the main docker github repository is a script called mkimage which does what it says on the tin.

<pre>sudo ./mkimage.sh -t alovelltroy/debian debootstrap --include=ceph,quagga --variant=minbase jessie</pre>

## Build big and then sqaush it
* [docker-squash](http://jasonwilder.com/blog/2014/08/19/squashing-docker-images/)

# Kernel Namespaces
If all you want to do is isolate an executable, [systemd-nspawn](http://rich0gentoo.wordpress.com/2014/07/14/quick-systemd-nspawn-guide/) is awesome.

