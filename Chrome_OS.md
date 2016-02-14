---
title: Chrome OS
permalink: /Chrome_OS/
---

proot
-----

Download the [proot binary](http://static.proot.me/proot-x86_64) and [Nix](https://nixos.org/nix/download.html) to somewhere under `/usr/local`.

Run the install script to get a nix-profile in your home directory:

`./proot-x86_64 -b nix-1.8-x86_64-linux:/nix /nix/install`

You can then use Nix:

`./proot-x86_64 -b nix-1.8-x86_64-linux:/nix bash --init-file ~/.nix-profile/etc/profile.d/nix.sh`

The drawback to the proot approach is that you can't do anything with root inside of the shell.

chroot
------

buildFHSChrootEnv
-----------------