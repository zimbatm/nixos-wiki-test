---
title: Encrypted Root on NixOS
permalink: /Encrypted_Root_on_NixOS/
---

There are many possible encrypted root setups. This guide is for setting up LVM over Luks, which is an easy and safe setup that will satisfy most users. Other setups should still be sufficiently similar.

Important
---------

Here you will find \*only\* NixOS-specific bits. For general information regarding this setup, how to partition disks and such refer to [1](http://wiki.archlinux.org/index.php/System_Encryption_with_LUKS_for_dm-crypt) or other detailed guide.

Config
------

In this example we assume that:

-   your **boot partition** is **/dev/sda1** and
-   your **luks-encrypted partition** is **/dev/sda2** that contains the LVM volume group "vg".

This is what needs to be put into /etc/nixos/configuration.nix:

### Make sure initrd finds and mounts the root partition

      boot.initrd.luksRoot = "/dev/sda2";

If you need to decrypt additional devices, you can do it like this:

      boot.initrd.postDeviceCommands = "cryptsetup luksOpen /dev/sda3 lukshome";

Note: NixOS currently does support additional devices but only in combination with using "boot.initrd.luksRoot". That means if you only want to have an encrypted /home you still have to assign a boot.initrd.luksRoot="foobar" which preferably should not exist at all. This also means that you will get an error every boot as this device does not exist for obvious reasons.

A possible fix would be to change /etc/nixos/nixos/modules/system/boot/luksroot.nix like this:

`   boot.initrd.preLVMCommands = ''`
`     # Wait for luksRoot to appear, e.g. if on a usb drive.`
`     # XXX: copied and adapted from stage-1-init.sh - should be`
`     # available as a function.`
`     if [ ${luksRoot} != "postBootOnly" ]; then`
`          if ! test -e ${luksRoot}; then`
`              echo -n "waiting for device ${luksRoot} to appear..."`
`              for ((try = 0; try < 10; try++)); do`
`                  sleep 1`
`                  if test -e ${luksRoot}; then break; fi`
`                  echo -n "."`
`              done`
`              echo "ok"`
`          fi`
`          # open luksRoot and scan for logical volumes`
`          cryptsetup luksOpen ${luksRoot} luksroot`
`     fi`
`   '';`

While using boot.initrd.luksRoot="postBootOnly".

### Extra modules you might need

Depending on the encryption algorithm you have chosen for your Luks volume, you will probably need to ensure kernel has the necessary crypto modules.

If you are unsure which ones, run

    cryptsetup luksDump /dev/sda2

What you are looking for is this:

    LUKS header information for /dev/sda2

    Version:        1
    Cipher name:    aes
    Cipher mode:    cbc-essiv:sha256
    Hash spec:      sha1

In this example you need SHA1, SHA256, AES, and CBC.

You can put the required crypto modules into initrd along with dm_crypt module needed by Luks like this:

    boot.initrd.kernelModules = ["dm_crypt" "aes-i586" "sha256" "sha1" "cbc"];

On my 64bit system (2.6.32) I needed this modules instead the listed above:

    boot.initrd.kernelModules = ["dm_crypt" "sha256_generic" "sha1_generic" "cbc" "aes_x86_64" "aes_generic" "xts" ];

Note that some USB keyboards also need special kernel support, which must be available in the init ramdisk to enter the pass phrase. Recent models of the Microsoft Internet keyboard, for example, need the module `hid_microsoft` in order to be recognized as an input device.

### Root Filesystem

      fileSystems = [ {
        mountPoint = "/";
        device = "/dev/mapper/vg-root";
        #device = "/dev/mapper/luksroot"; # use this instead when NOT using LVM
      } {
        mountPoint = "/boot";
        device = "/dev/sda1";
      }
      #other filesystems go here
      ]

Mounting from a rescue system
-----------------------------

Within the rescue system:

      # cryptsetup luksOpen /dev/sda2 crypt
      # vgscan
      # vgchange -ay

Now your logical volumes should be available. XXX: written from memory, please correct or remove this sentence.