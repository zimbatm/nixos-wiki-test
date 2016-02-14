---
title: Talk:How to install NixOS from Linux
permalink: /Talk:How_to_install_NixOS_from_Linux/
---

At <https://nixos.org/wiki/How_to_install_NixOS_from_Linux#Modifying_your_bootloader.27s_config> "some_dir" should be gone and this presumes that the entry has already been made.

To make the entry I edited /etc/grub.d/custom_40 and added the following

menuentry "NixOS Installer / Rescue" {

` linux /nixos-livecd-bzImage init=/nix/store/p5n72ay1c1wx4wry90zabr8jnljpdzgx-nixos-0.2pre4601_1def5ba-48a4e91/init root=LABEL=NIXOS_0.2pre4601_1def5ba-48a4e91`
` initrd /nixos-livecd-initrd.img`

}

Note that grub doesn't seem to need one to enter the "/boot" part, but for intird you need to specify its file type, which is supposed to be .img (so open up your file manager and change "nixos-livecd-initrd" to "nixos-livecd-initrd.img")

As for chroot from Arch <https://nixos.org/wiki/How_to_install_NixOS_from_Linux#Starting_the_install> it tells you to edit the init script but since squashfs has mounted it to /dev/loop0 which is write protected I don't think that was thought through all the way.

(chroot) password
-----------------

It seems to me that the following is misleading. Changing the root password in the chroot doesn't set it in the newly created install. We might suggest changing the /etc/shadow file instead or creating a user with a password.