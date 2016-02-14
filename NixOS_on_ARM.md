---
title: NixOS on ARM
permalink: /NixOS_on_ARM/
---

Introduction
------------

Installation images and miscellaneous boot files for ARM devices are built & hosted by [@dezgeg](https://github.com/dezgeg) at `http://nixos-arm.dezgeg.me/installer`.

Also, a binary cache containing a subset of the unstable channel is hosted at `http://nixos-arm.dezgeg.me/channel` (signed with key `<nowiki>nixos-arm.dezgeg.me-1:xBaUKS3n17BZPKeyxL4JfbTqECsT+ysbDJz29kLFRW0=%</nowiki>`).

Supported devices
-----------------

The installation images have been tested on the following devices:

| Manufacturer            | Board         | SoC           | ISA   | CPU                     | RAM        | Storage                  |
|-------------------------|---------------|---------------|-------|-------------------------|------------|--------------------------|
| NVIDIA                  | Jetson TK1    | Tegra K1/T124 | ARMv7 | 4x Cortex-A15 @ 2.3 GHz | 2 GB       | 16 GB eMMC, SD, SATA     |
| Linksprite              | pcDuino3 Nano | Allwinner A20 | ARMv7 | 2x Cortex-A7 @ 1 GHz    | 1 GB       | 4 GB NAND, microSD, SATA |
| Raspberry Pi Foundation | Raspberry Pi  | BCM2835       | ARMv6 | 1x ARM1176 @ 700 MHz    | 256/512 MB | SD/microSD               |

Additionally, emulation is supported via QEMU's vexpress-a9 target.

Installation
------------

The installation images come in two flavors: [sd-image-armv6l-linux.img](http://nixos-arm.dezgeg.me/installer/sd-image-armv6l-linux.img) is built for the ARMv6 architecture and it comes with the Raspberry Pi kernel. [sd-image-armv7l-linux.img](http://nixos-arm.dezgeg.me/installer/sd-image-armv7l-linux.img) is built for the ARMv7 architecture and comes with the mainline multiplatform ARMv7 kernel (`multi_v7_defconfig`). Make sure you download the correct image for your board!

The `.img` files can be directly written to a microSD/SD card (minimal recommended size: 4 GB) using `dd`. For example, if your SD card shows up as `/dev/sdc`:

`sudo dd if=sd-image-armv7l-linux.img of=/dev/sdc`

(As always, be careful when using dd so you don't wipe your hard drive!)

Board-specific installation notes
---------------------------------

Depending on the board, some additional preparation steps might be needed to make the SD card bootable on your device.

### Jetson TK1

The proprietary NVIDIA bootloader can only boot NVIDIA's L4T kernel, so it needs to be replaced by flashing U-Boot on the board's eMMC via the recovery USB port. The easiest way to do that is to use [tegra-uboot-flasher-scripts](https://github.com/NVIDIA/tegra-uboot-flasher-scripts), though unfortunately that's currently not packaged in NixOS. Once U-Boot is flashed, the ARMv7 image will boot out-of-the-box.

### pcDuino 3 Nano

U-Boot needs to be copied to the microSD card with dd, with parameters `bs=1024 seek=8`. Download U-Boot for the board ([uboot-Linksprite_pcDuino3_Nano_defconfig-2015.07_u-boot-sunxi-with-spl.bin](http://nixos-arm.dezgeg.me/installer/uboot-Linksprite_pcDuino3_Nano_defconfig-2015.07_u-boot-sunxi-with-spl.bin)), and copy it to the correct location with (assuming the SD card still shows up as `/dev/sdc`):

`sudo dd if=uboot-Linksprite_pcDuino3_Nano_defconfig-2015.07_u-boot-sunxi-with-spl.bin of=/dev/sdc bs=1024 seek=8`

### Raspberry Pi

The ARMv6 image boots out-of-the-box! Note that Raspberry Pi 2 is not yet supported by these images, but in the future the ARMv7 image will work.

### Versatile Express (Cortex-A9) in QEMU

First, convert the downloaded [sd-image-armv7l-linux.img](http://nixos-arm.dezgeg.me/installer/sd-image-armv7l-linux.img) to a new qcow2 formatted disk image (for example, `arm-vm.qcow2`) and resize it to the desired size (for example, 2.5G):

`qemu-img convert -O qcow2 sd-image-armv7l-linux.img arm-vm.qcow2`
`qemu-img resize arm-vm.qcow2 2.5G`

Then, to boot the VM, download U-Boot for the Versatile board ([uboot-vexpress_ca9x4_defconfig-2015.07_u-boot](http://nixos-arm.dezgeg.me/installer/uboot-vexpress_ca9x4_defconfig-2015.07_u-boot)) and run:

`qemu-system-arm -kernel uboot-vexpress_ca9x4_defconfig-2015.07_u-boot -M vexpress-a9 -serial stdio -sd arm-vm.qcow2 -m 512`

The amount of memory (`-m 512`) should be at least 512 megabytes, or you will get all sorts of funny behaviour if/when U-Boot tries to access nonexistent RAM. Unfortunately, the maximum amount of memory is limited to 1024 megabytes.

NixOS installation & configuration
----------------------------------

The installation image is actually a MBR partition table plus two partitions; a FAT32 `/boot` and a ext4 root filesystem. The image is designed such that it's possible to directly reuse the SD image's partition layout and "install" NixOS on the very same SD card by simply replacing the default `configuration.nix` and running `nixos-rebuild`. Using this installation method is **strongly** recommended, though if you know exactly what you're doing and how U-Boot on your board works, you can use `nixos-install` as usual. To help with the SD card installation method, the boot scripts on the image automatically resize the rootfs partition to fit the SD card on the first boot.

Use this as a template:

`{ config, pkgs, lib, ... }:`
` {`
`   # NixOS wants to enable GRUB by default`
`   boot.loader.grub.enable = false;`
`   # Enables the generation of /boot/extlinux/extlinux.conf`
`   boot.loader.generic-extlinux-compatible.enable = true;`
` `
`   # !!! If your board is a Raspberry Pi, select this:`
`   boot.kernelPackages = pkgs.linuxPackages_rpi;`
`   # !!! Otherwise, pick this:`
`   boot.kernelPackages = pkgs.linuxPackages_latest;`
` `
`   # Manual doesn't currently evaluate on ARM`
`   services.nixosManual.enable = false;`
` `
`   nix.binaryCaches = lib.mkForce [ "http://nixos-arm.dezgeg.me/channel" ];`
`   nix.binaryCachePublicKeys = [ "nixos-arm.dezgeg.me-1:xBaUKS3n17BZPKeyxL4JfbTqECsT+ysbDJz29kLFRW0=%" ];`
` `
`   # File systems configuration for using the installer's partition layout`
`   fileSystems = {`
`     "/boot" = {`
`       device = "/dev/disk/by-label/NIXOS_BOOT";`
`       fsType = "vfat";`
`     };`
`     "/" = {`
`       device = "/dev/disk/by-label/NIXOS_SD";`
`       fsType = "ext4";`
`     };`
`   };`
` `
`   # !!! Adding a swap file is optional, but strongly recommended!`
`   # swapDevices = [ { device = "/swapfile"; size = 1024; } ];`
` }`

**Note:** the default `configuration.nix` will contain something like `imports = [ <nixos/modules/installer/cd-dvd/sd-image-armv7l-multiplatform.nix> ];` do **not** include that in your final installation or you will experience interesting problems. It is only for building the installation image!

To make the ARM experience slightly less painful, the config template adds `nixos-arm.dezgeg.me` as a binary cache, which contains a small subset of packages on the unstable channel (though a caution for US users: the server hosting them is physically located in Finland). Note that the binary cache isn't enabled on the prebuilt images, so enable it via the command line when building for the first time:

`nixos-rebuild switch --fast --option binary-caches `[`http://nixos-arm.dezgeg.me/channel`](http://nixos-arm.dezgeg.me/channel)` --option binary-cache-public-keys nixos-arm.dezgeg.me-1:xBaUKS3n17BZPKeyxL4JfbTqECsT+ysbDJz29kLFRW0=%`

Details about the boot process
------------------------------

On NixOS, all ARM boards use the popular U-Boot as the bootloader and U-Boot's [Generic Distro Configuration Concept](https://github.com/u-boot/u-boot/blob/master/doc/README.distro) as the mechanism to communicate boot information (such as path to kernel `zImage`, `initrd`, DTB, command line arguments). For a quick TL;DR about the generic distro configuration support: U-Boot is scripted to scan all attached storage devices & partitions and look for a file named `/extlinux/extlinux.conf` or `/boot/extlinux/extlinux.conf` (which will be generated by NixOS, just like `/boot/grub/grub.cfg` is generated on PCs).

U-Boot also provides an interactive shell and the generation selection menu (just like GRUB). However, support for input or display devices varies greatly, depending on the board:

### Jetson TK1

Only serial console (via the DB-9 connector) is supported.

### pcDuino 3 Nano

USB keyboards and HDMI displays work perfectly! Also a 3.3v serial port via the 3-pin header works.

### Raspberry Pi

HDMI displays work, but sadly USB keyboards don't. A 3.3v serial port is via the 26-pin connector works.

### Versatile Express (Cortex-A9) in QEMU

The emulated serial port (connected to QEMU's stdin/stdout) works.

Porting NixOS to new boards
---------------------------

### The easy way (if you're lucky)

If your board is an ARMv7 board supported by [multi_v7_defconfig](/NixOS_on_ARM/multi_v7_defconfig "wikilink") and you have access to U-Boot on the board, getting [sd-image-armv7l-linux.img](http://nixos-arm.dezgeg.me/installer/sd-image-armv7l-linux.img) to boot is the easiest option:

-   If you're lucky and your U-Boot build comes with the `extlinux.conf` support built in, the image boots out-of-the-box. This is the case for all (upstream) Allwinner and Tegra U-Boots, for instance.
-   Otherwise, you can get the boot information (path to kernel `zImage`, `initrd`, DTB, command line arguments) by extracting `extlinux.conf` from the boot partition of the image, and then attempt to boot it via the U-Boot shell, or some other mechanism that your board's distro uses (e.g. `uEnv.txt`).

### The hard way

Alternatively/if all else fails, you can do it the hard way and [bootstrap NixOS from an existing ARM Linux installation](/Bootstrapping_NixOS_on_ARM "wikilink").

### Contributing new boards to nixpkgs

-   Add a new derivation for your board's U-Boot configuration, see for example [ubootJetsonTK1 in all-packages.nix](https://github.com/NixOS/nixpkgs/blob/e196be4bc01bd8ea71ad2d7b258b1c72edb87ebb/pkgs/top-level/all-packages.nix#L10128).
-   If your board's U-Boot configuration doesn't use the `extlinux.conf` format by default, create a patch to enable it. Some C hacking skills & U-Boot knowledge might be required. For some pointers, see [this patch to enable it on the Versatile Express](https://github.com/NixOS/nixpkgs/blob/02f9f0c7e06538ad9974d34933d7299b51e8ef6c/pkgs/misc/uboot/vexpress-Use-config_distro_bootcmd.patch).
-   Make a pull request, also containing the board-specific instructions. Ping `@dezgeg` for review and for building & hosting the U-Boots at <http://nixos-arm.dezgeg.me/installer>.
