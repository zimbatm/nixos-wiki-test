---
title: Installing VirtualBox on NixOS
permalink: /Installing_VirtualBox_on_NixOS/
---

This page describes how to run VirtualBox on NixOS.

Enabling VirtualBox
-------------------

|                                                                                                                                                                                                   |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **WARNING: VirtualBox is currently built with the "--disable-hardening" flag. This is a security risk and we need to fix it. See <https://www.virtualbox.org/wiki/Linux%20build%20instructions>** |

Add the following to your `configuration.nix`:

    imports = [ <nixos/modules/programs/virtualbox.nix> ];

Then run:

    $ nixos-rebuild switch

VirtualBox is only available to users in the `vboxusers` group. So to enable VirtualBox for user `alice`, do:

    $ usermod -a -G vboxusers alice

### Enabling VirtualBox Extension Pack

This is necessary for the USB subsystem and remote desktop access. Unfortunately, the extension pack is behind a license agrreement, you have to download it manually:

Get *Oracle_VM_VirtualBox_Extension_Pack-4.2.12-84980.vbox-extpack* from <https://www.virtualbox.org/wiki/Downloads>.

Then add it to the Nix store:

    nix-prefetch-url file://Oracle_VM_VirtualBox_Extension_Pack-4.2.12-84980.vbox-extpack

Override the VirtualBox package to enable the extension pack:

    boot.kernelPackages = pkgs.linuxPackages // {
      virtualbox = pkgs.linuxPackages.virtualbox.override {
        enableExtensionPack = true;
      };
    };

(Replace *pkgs.linuxPackages* with the package set you want to use)

or set nixos option:

      nixpkgs.config.virtualbox.enableExtensionPack = true;

Switch to the new system configuration:

    nixos-rebuild switch

This will take a long time because Nix will compile VirtualBox from source.

Running VirtualBox
------------------

Just do:

    $ VirtualBox

Note since there is no setuid root you can not ping from the guest machines!!

Error messages
--------------

Since this expression implements the open source VirtualBox it **does not include VirtualBox Extension Pack(s)**.

### Failed to access the USB subsystem

The message:

`Failed to access the USB subsystem.`
`VirtualBox is not currently allowed to access USB devices. You can change this by allowing your user to access the 'usbfs' folder and files. Please see the user manual for a more detailed explanation.`

Shows that you do not have access to the usbfs. **Such USB access might require Oracle VirtualBox (non-free) and the VirtualBox Extension Pack installed** on the Host computer, as well as the user being part of the vboxusers group.

### Missing kernel modules

If you get this error message (like this), something went wrong:

    /dev/vboxdrv does not exist. Load the kernel module then try again.

If in doubt, try rebooting to make sure the kernel module is loaded.

### too many vboxnet adapters

for some unknown reason i had 105 vboxnet adapters like:

`   ip a`
`   vboxnet0, vboxnet1, vboxnet2, vboxnet.., vboxnet105 `

but i removed most of them with this command:

``    for i in `seq 1 105`; do VBoxManage hostonlyif remove vboxnet$i; done ``

[Category:Virtualization](/Category:Virtualization "wikilink") [Category:Applications](/Category:Applications "wikilink") [Category:Configuration](/Category:Configuration "wikilink")