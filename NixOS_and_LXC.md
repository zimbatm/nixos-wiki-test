---
title: NixOS and LXC
permalink: /NixOS_and_LXC/
---

If you want to install ubuntu template you need to install debootstrap and the lxc.

You also need to create /var/lib/lxc and /var/cache/lxc directories.

then if you run

sudo lxc-create -l DEBUG -n guestname -t ubuntu

it fails because it can not delelete the /proc from the /var/cache/lxc

in progress

If you want to use LXC on top of libvirt, use this [NixOS_and_libvirt](/NixOS_and_libvirt "wikilink")