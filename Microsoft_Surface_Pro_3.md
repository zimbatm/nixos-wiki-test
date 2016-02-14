---
title: Microsoft Surface Pro 3
permalink: /Microsoft_Surface_Pro_3/
---

Work in progress. This is the setup for a dual-boot windows/NixOS.

Installation
------------

Current requirements:

-   USB stick
-   Latest unstable minimal installation iso, I used [<https://nixos.org/releases/nixos/unstable/nixos-15.05pre61665.4c01e6d/nixos-minimal-15.05pre61665.4c01e6d-x86_64-linux.iso> the 15.06-pre one](/https://nixos.org/releases/nixos/unstable/nixos-15.05pre61665.4c01e6d/nixos-minimal-15.05pre61665.4c01e6d-x86_64-linux.iso_the_15.06-pre_one "wikilink")
-   (maybe) USB keyboard and therefore a USB hub
-   WiFi SSID and password

In the current installation iso, the kernel doesn't support the MS Type cover yet, so you need a USB keyboard for the initial install. Also note that at one point the keyboard wasn't working, and I couldn't see it in lsusb output. After a cold boot it worked fine.

### Steps

Preparation:

-   Copy the ISO to the USB stick as explained [<https://nixos.org/nixos/manual/sec-installation.html#sec-booting-from-usb> in the manual](/https://nixos.org/nixos/manual/sec-installation.html#sec-booting-from-usb_in_the_manual "wikilink"). Note that if you just dd the image onto the USB stick, it will boot without changes
-   With Windows Disk Management, shrink the windows disk so you have room for your installation. Use the free space to create a new partition without a filesystem or drive letter.

<!-- -->

-   Reboot the Surface and furiously tap Esc until you end up in the BIOS screen. There, turn off secure boot (don't delete the keys), and change the boot order to USB + SSD. Save.
-   You should now be in the NixOS installer
-   Attach the USB stick and keyboard if the Type Cover doesn't work. The NixOS manual is at alt+F8

Installation:

-   Using gdisk, print your partition table to find the empty partition you created. Find the EFI partition number (1 for me) and your new partition number (5 for me).
-   Create your filesystem of choice on the partition, I picked btrfs: \`mkfs.btrfs -L root /dev/disk/by-id/xxx-5\` (xxx is the samsung ssd). Mount it on /mnt.
    -   With btrfs it's wise to put the nix store in its own subvolume: \`btrfs subvol create /mnt/nix; btrfs subvol create /mnt/nix/store\`
-   We'll be using Gummiboot, which needs to access the EFI partition. \`mkdir /mnt/boot; mount /dev/disk/by-id/xxx-1\`
-   Initialize your hardware configuration: \`nixos-generate-config --root /mnt\`
-   Create your wifi credentials: \`wpa_passphrase SSID PASS &gt; /etc/wpa_supplicant.conf; cp /etc/wpa_supplicant.conf /mnt/etc\`. Check that ifconfig gets an ip address.
-   Edit /mnt/etc/configuration.nix, making sure that you have at least:

`  networking.wireless.enable = true;  # Enables wireless support via wpa_supplicant.`
``
`  boot.kernelPackages = pkgs.linuxPackages_latest;`
`  # be able to use the type cover in the initrd`
`  boot.kernelModules = [ "hid-microsoft" ];`

-   Install: \`nixos-install\`

If this completes successfully, you should be able to reboot, remove the USB stick, and NixOS should come up.

**Note**: Gummiboot doesn't support the Type Cover, so if you want to boot into windows while on the go, you need to bring a USB keyboard. You can also tell gummiboot to boot windows by default and have a USB keyboard around to boot NixOS, by setting some setting, please add here.