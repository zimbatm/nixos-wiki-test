---
title: Talk:Install NixOS on Linode
permalink: /Talk:Install_NixOS_on_Linode/
---

1.  Rescue disk does not provide nixbld group and users (nixbld1, etc). You have to create them manually
2.  nixos-install tries to run passwd under chroot. Copy passwd to /mnt/bin/ from /mnt/nix/store/...

--[Pin](/User:Pin "wikilink") ([talk](/User_talk:Pin "wikilink")) 23:23, 14 July 2014 (CEST)