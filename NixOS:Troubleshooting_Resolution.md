---
title: NixOS:Troubleshooting Resolution
permalink: /NixOS:Troubleshooting_Resolution/
---

Booting from USB does not work
------------------------------

**Issue:** When booting from USB, in stage 1 of the booting process, the following text comes up several times:

`
    File descriptor 3 (/nix/store/...-initrd-fsinfo) leaked on lvm invocation. Parent PID 1: /nix/store/myj2p16wdpnbmb53iib3
    No volume groups found.
`

And after this, the boot process fails with

`
    mounting /dev/root on /iso...
    mount: mounting /dev/root on /mnt-root/iso failed: No such file or directory
    An error occured in stage 1 of the boot process, which must mount the root filesystem on /mnt-root and then start stage 2. Press one of the following keys:
    r) to reboot immediately
    *) to ignore the error and continue
`

**Solution:** Boot the fallback image.

**Tested on:** Lenovo Thinkpad X220 UEFI boot.