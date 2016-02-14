---
title: NixOS and libvirt
permalink: /NixOS_and_libvirt/
---

To use tools such as virt-manager or virsh in a standard user mode, your user must be in the group libvirtd.

    sudo gpasswd -a myuser libvirtd
    su myuser
    newgrp libvirtd

Edit your /etc/nixos/configuration.nix and add :

    virtualisation.libvirtd.enable = true;