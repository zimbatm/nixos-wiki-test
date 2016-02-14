---
title: Install NixOS on Linode
permalink: /Install_NixOS_on_Linode/
---

The high level approach that these directions will take is to boot into the Rescue Mode, then mount the partitions we'll install on, install NixOS and NixPkgs on the Rescue Disk and then have it install to the mounted partition we actually want it installed on.

Setup your Disk Image partions.
-------------------------------

### Create Disk Images in Linode Manager

You will need:

-   A small partition we will call 'boot', it should be ~200 mb this will be */dev/xvda*
-   Make the rest of your disk space one other parition, we'll call this 'nixos' this will be */dev/xvdb*

Additionally, be sure to choose the "pv_grub-<arch>" kernel to actually use the kernel supplied from the nixos installation.

### Boot into Rescue Mode and use LISH to connect to your machine

What will become /boot needs to be ext2, I make my main partition ext4:

`mkfs.ext2 /dev/xvda`
`mkfs.ext4 /dev/xvdb`

### Add a filesystem label to the NixOS Installation Disk

This is optional. The label is used in configuration.nix later so make sure to adapt it to your needs.

`e2label /dev/xvdb nixos`

### Mount the Partitions

`mount /dev/xvdb /mnt`
`mkdir /mnt/boot`
`mount /dev/xvda /mnt/boot`

Install Prerequisites
---------------------

### Install Nix in the Rescue System

Installing Nix as root doesn't work, so create an user just to do that:

`adduser nix # set password`

We also need the Nix build users to exist:

`groupadd -r nixbld`
`for n in $(seq 1 10); do useradd -c "Nix build user $n" \`
`    -d /var/empty -g nixbld -G nixbld -M -N -r -s "$(which nologin)" \`
`    nixbld$n; done`

Now do the Nix install:

`mkdir /nix`
`chown -R nix /nix`
`su - nix`
`bash <(curl `[`https://nixos.org/nix/install`](https://nixos.org/nix/install)`)`
`exit`

And then for the rest we can be root again.

`. ~nix/.nix-profile/etc/profile.d/nix.sh `
`nix-channel --remove nixpkgs`
`nix-channel --add `[`http://nixos.org/channels/nixos-14.12`](http://nixos.org/channels/nixos-14.12)` nixos`
`nix-channel --update`

### Install vim for editing (Optional)

`nix-env -i vim`

### Install *nixos-install*, *nixos-option*, and *nixos-hardware-schan*

`cat <`<EOF >` configuration.nix`
`{ fileSystems."/" = {};`
`  boot.loader.grub.enable = false;`
`} `
`EOF`

`export NIX_PATH=nixpkgs=/root/.nix-defexpr/channels/nixos:nixos=/root/.nix-defexpr/channels/nixos/nixos`
`export NIXOS_CONFIG=/root/configuration.nix`
`nix-env -i -A config.system.build.nixos-install -A config.system.build.nixos-option -A config.system.build.nixos-generate-config -f "`<nixos>`"`

Configure your system
---------------------

### Generate configuration.nix template in /mnt/etc/nixos/

`export NIX_PATH=nixpkgs=/root/.nix-defexpr/channels/nixos:nixos=/root/.nix-defexpr/channels/nixos/nixos`
`nixos-generate-config --root /mnt`

Edit your */mnt/etc/nixos/configuration.nix*
--------------------------------------------

These are the minimal changes you'll have to make, you probably also want to turn sshd on, and perhaps create a user. That disk and swap settings in *hardware-configuration.nix* should be correct but you should check to confirm.

` # Use the GRUB 1 boot loader.`
` boot.loader.grub = {`
`   enable = true;`
`   version = 1;`
`   extraPerEntryConfig = "root (hd0)";`
`   device = "nodev";`
` };`

If you want a swap file, add something like:

` swapDevices = [`
`   { device = "/var/swapfile";`
`     size = 2048; # in MB`
`   }`
` ];`

You could also use a swap partition, but there's no reason to since the performance is the same[1](http://lkml.iu.edu/hypermail/linux/kernel/0507.0/1690.html) and swap partitions are less flexible.

Install NixOS!
--------------

`unset NIXOS_CONFIG`
`nixos-install`

### Fix Grub

Because of how Linode will try to load grub on the /boot partition, we need to make this symlink so it can find it:

`mkdir -p /mnt/boot/boot/grub`
`cd /mnt/boot/boot/grub`
`ln -sv ../../grub/menu.lst /mnt/boot/boot/grub`

### Set Root Password

You should set the root password here. If you forget, you can use Linode's "Reset Root Password" option in the Rescue tab after finishing the install.

Reboot to NixOS
---------------

The installation is finished. The last steps before booting the system is creating a configuration profile in the Linode Manager:

-   Boot Settings
    -   Kernel: pv-grub-x86_64
-   Block Device Assignment
    -   /dev/xvda: Your 'boot' image
    -   /dev/xvdb: nixos
    -   root device: /dev/xvdb

Change other configuration options to your preference.

Now hit *boot* in the Linode Manager and wait for NixOS to boot. You can check progress in the Remote Access Console.

Update Channels to finish installation
--------------------------------------

`nix-channel --update`

You now should have a fully functional NixOS install on Linode, enjoy!

Using the upgraded KVM Linode Direct Disk boot and grub 2 (with GPT/BIOS)
-------------------------------------------------------------------------

This section explains the key differences if you are upgrading to use Linode's new KVM virtualization which does not give you the pv-grub-x86_64 boot option.

From a high level perspective, this just means that we can simply use one single disk and partition that single disk to contain BIOS, boot, root, swap. We can use a swapfile as well but in this example, I will just set up a swap partition. A swap is necessary if you are using a linode VPS with limited ram (1 GB) because nix package manager will complain about running out-of-memory when it needs to bootstrap our initial installers.

The second important point is that we need to ensure that we have the correct kernel modules specified so that our linode host can boot up our instance in the KVM environment.

This is the key reference doc that we need - <https://linode.com/docs/platform/kvm>

Other than these two key differences, and ignoring the section "Fix Grub" above (which is intended for installing grub legacy that works with pv-grub-x86-84), the steps for using the rescue system (which is finnix on linode), bootstrapping and generating our initial \`configuration.nix\` and \`hardware-configuration.nix\` will be the same as that mentioned in the previous sections.

### Create Disk Partitions

In this example, I will just use \`/dev/sda\` as the single disk which I will partition further. Note that in linode's KVM environment, \`/dev/xvd\*\` have also been changed to \`/dev/sd\*\`.

After creating our disk on linode manager, we boot into rescue mode (finnix) and prepare our partitions:

` gdisk /dev/sda`

Here's the partitions I created:

` Number  Start (sector)    End (sector)  Size       Code  Name`
`    1            2048            4095   1024.0 KiB  EF02  BIOS boot partition`
`    2            4096         1028095   500.0 MiB   8300  Linux filesystem`
`    3         1028096         3125247   1024.0 MiB  8200  Linux swap`
`    4         3125248        50331614   22.5 GiB    8300  Linux filesystem`

Note that the BIOS boot partition with \`EF02\` is mandatory because the direct disk boot approach requires that you set aside this sector for your BIOS.

After creating the partitions with gdisk, let's format them:

` mkfs.ext4 -L nixos /dev/sda4`
` mkfs.ext4 -L boot /dev/sda2`
` mkswap -L swap /dev/sda3`
` swapon /dev/sda3  # switch on the swap partition so nixos-install doesn't fail with out of memory error`

With this done, we follow the exact steps described above to create a temporary nix user, nixbld users, create the necessary \`/nix\` directories, curl install nix and nixos-generate-config.

### Manually update our configuration.nix and/or hardware-configuration.nix

The next important step is to manually ensure that our \`hardware-configuration.nix\` and \`configuration.nix\` uses the correct kernel modules (i.e. the files generated by \`nixos-generate-config --root /mnt\`).

We need to change our \`hardware-configuration.nix\`:

` imports =`
`   [ <nixpkgs/nixos/modules/installer/scan/not-detected.nix>`
`   ];`

to

` imports = `
`   [ <nixpkgs/nixos/modules/profiles/qemu-guest.nix>`
`   ];`

Which essentially imports us this - <https://github.com/NixOS/nixpkgs/blob/master/nixos/modules/profiles/qemu-guest.nix>, which means that all the correct \`virtio\` kernel modules required by qemu-kvm will be loaded at boot-time, i.e. explicitly:

` boot.initrd.availableKernelModules = [ "virtio_net" "virtio_pci" "virtio_blk" "virtio_scsi" "9p" "9pnet_virtio" ];`
` boot.initrd.kernelModules = [ "virtio_balloon" "virtio_console" "virtio_rng" ];`
` boot.initrd.postDeviceCommands =`
`   ''`
`     # Set the system time from the hardware clock to work around a`
`     # bug in qemu-kvm > 1.5.2 (where the VM clock is initialised`
`     # to the *boot time* of the host).`
`     hwclock -s`
`   '';`

If you are not using the head version of nixpkgs when bootstrapping nix above (e.g. if you were using nixos-14.12 per the original bootstrapping steps), you will need to manually ensure that this

` boot.initrd.availableKernelModules = [ "virtio_scsi" ];`

is included in your \`configuration.nix\` as well; because the older versions of \`qemu-guest.nix\` missed out this "virtio_scsi" flag which will cause the initrd to fail to detect and fail to mount your disk when booting up.

That's pretty much it.

[Category:Deployment](/Category:Deployment "wikilink")