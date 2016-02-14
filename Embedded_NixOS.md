---
title: Embedded NixOS
permalink: /Embedded_NixOS/
---

    <_deepfire-laptop> viric, I wonder if we could actually make an embedded spinoff of NixOS
    <_deepfire-laptop> viric, some generic infrastructure that everybody could use
    <_deepfire-laptop> viric, you probably know of OpenEmbedded/Yocto
    <_deepfire-laptop> viric, I think they suck big time, compared to what nixos could provide
    <_deepfire-laptop> Fragile, overcomplicated -- that's about Yocto
    <bjornfor> _deepfire-laptop, I agree about Yocto... let's make nixos better than yocto for building embedded images!
    <viric> _deepfire-laptop: nix-store -qR your_buildenv_path will give you the list of store paths you need
    <viric> put all those in a chroot, and all you put in the nixpkgs buildEnv will be there working.
    <_deepfire-laptop>  viric, i.e. merging contents of the these store paths would work?
    <viric> yes
    <viric> that's what buildEnv does
    <viric> iirc
    <viric> a nixpkgs function

- 2012, Using Nix to build embedded linux firmware: <http://thread.gmane.org/gmane.linux.distributions.nixos/8630>

- 2010, NixOS on embedded systems: <http://thread.gmane.org/gmane.linux.distributions.nixos/4539>