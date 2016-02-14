---
title: Lenovo Thinkpad Helix
permalink: /Lenovo_Thinkpad_Helix/
---

Lenovo ThinkPad Helix
=====================

Overview
--------

| Component | Hardware              |
|-----------|-----------------------|
| Graphics  | Intel HD 4000         |
| Wireless  | Intel 6205 Advanced-N |
| Kernel    | 3.8                   |
||

Preparing to boot nixos
-----------------------

In BIOS, disable Secure boot and enable legacy boot (non-UEFI).

Tips & Tricks
-------------

### Disable speaker

Add this to your /etc/nixos/configuration.nix.

`boot.blacklistedKernelModules = [ "pcspkr" ];`