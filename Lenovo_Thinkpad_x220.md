---
title: Lenovo Thinkpad x220
permalink: /Lenovo_Thinkpad_x220/
---

When installing NixOS on a Lenovo Thinkpad x220, the wiki entry for the [Lenovo Thinkpad x200](/Lenovo_Thinkpad_x200 "wikilink") can help a lot.

The following configurations work on a Thinkpad X220, using gummiboot as bootloader:

`   boot.loader.gummiboot.enable = true;`
`   boot.loader.efi.canTouchEfiVariables = true;`

`   boot.initrd.availableKernelModules = [ "ehci_pci" "ahci" "usb_storage" ];`
`   boot.initrd.kernelModules = [ "fbcon" "kvm-intel" ];`

For encrypted hard drives, the following options are required:

`   boot.initrd.luks.cryptoModules = [ "aes" "sha256" "sha1" "cbc" ];`