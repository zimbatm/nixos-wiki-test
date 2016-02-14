---
title: User:Havvy Linode
permalink: /User:Havvy/Linode/
---

-   -   This is a WIP\*\*\*. See [Linode](/Linode "wikilink") for the non-WIP instructions. Read that whole page before starting if you're using the KVM hypervisor.

This tutorial is written for people who want to run NixOS on a Linode instance. We assume you are using the KVM hypervisor instead of the older hypervisor, Xen.

In this tutorial, we will show you how to set up NixOS on Linode by setting up disk, installing Nix onto the rescue OS, and use Nix to install NixOS onto your disks.

Create a Linode Instance
------------------------

Linode uncreatively calls each virtual server a 'Linode'' itself. We'll be calling them a *Linode instance*. Create one of whichever size you prefer and wherever you prefer to locate it. Linode will direct you to the *Dashboard* tab of the *[Linode Manager](https://manager.linode.com)*.

Setup Disks
-----------

### Create Disk Images

On the *Dashboard* tab, there is an option *Create a new disk*. You will need to create a single disk.

-   label: nixos
-   type: ext4
-   size: Maximum

*Note*: The label is arbitrary.

### Boot into Finnix

Now that you have the disks created, it's time to boot the Linode instance. Go to the *Rescue* tab in the *Linode Manager*, and select *Reboot into Rescue Mode*, which uses Finnix as an OS.

Now that it's started, connect to it via *SSH*. In the *Remote Access* tab of the *Linode Manager*, there's either *Lish via Ajaxterm* or *Lish via SSH*. The *SSH* option is much more pleasant to use since Ajaxterm disconnects after a couple minutes of idle. Pick one, and connect. If it asks for a password, give your Linode password.

Once in, it'll say `Welcome to Finnix!` amongst other things.

*Note*: The Lish shell does not let you scroll back and if you write or paste a line that is longer than the line length, it'll write over the beginning of your line visually. Even if you ssh into it through a terminal that doesn't have these issues, you will get them. Just be glad you only need to use Lish to set up NixOS, not use it.

### Repartitioning

With this single disk, we are going to repartition it into four different disks: A disk for `grub`, a boot disk, a swap disk, and the actual nixos disk. To do this, we will use `gdisk`. Without a swap disk, the actual installation with run out of memory.

` gdisk /dev/sda`

To create new partitions, type `n` and follow the instructions. The following table shows the values you need. When finished, write your changes and exit with `w`. This will create four devices, `/dev/sda1` through `/dev/sda4`.

` Number  Start (sector)    End (sector)  Size       Code  Name`
`    1            2048            4095   1024.0 KiB  EF02`<sup>`A`</sup>` BIOS boot partition`
`    2            4096         1028095   500.0 MiB   8300  Linux filesystem`
`    3         1028096         3125247   1024.0 MiB  8200  Linux swap`
`    4         3125248        50331614`<sup>`B`</sup>` 22.5 GiB    8300  Linux filesystem`

**A**: Note that the BIOS boot partition with \`EF02\` is mandatory because the direct disk boot approach requires that you set aside this sector for your BIOS.

**B**: The end sector for the final partition is dependent upon the size of the Linode you are creating. This sector is the final sector on the smallest available Linode instance.

After creating, we now need to format them, since `gdisk` does not actually format them.

`mkfs.ext4 -L nixos /dev/sda4`
`mkfs.ext4 -L boot /dev/sda2`
`mkswap -L swap /dev/sda3`

### Mounting

The devices now need to be mounted, and swap turned on.

`mount /dev/sda4 /mnt`
`mkdir /mnt/boot`
`mount /dev/sda2 /mnt/boot`
`swapon /dev/sda3`

Install Nix
-----------

We are going to install *Nix* into your *Rescue System* and use that to install *NixOS* onto your `nixos` disk. *Nix* can be used without *NixOS*, and that is what we are doing here - installing *Nix* so that it can install *NixOS* which will come with its own *Nix*.

### Create Temp User

Installing Nix as root does not work. Create a new user so that you can install Nix. This new user will **not** exist once the process is finished.

We will also need a group `nixbld` and a set of users in that group.

adduser nix \# you will need to set a password - it will never be asked for

groupadd -r nixbld for n in $(seq 1 10); do useradd -c "Nix build user $n" \\

`   -d /var/empty -g nixbld -G nixbld -M -N -r -s "$(which nologin)" \`
`   nixbld$n; done`

</pre>
*Note*: If you paste this all in at once, only the `adduser` command will execute. Do the `adduser` command separately from the other commands.

### Download and Build

Now, to install *Nix* itself, we need to set up an environment to install in and then download and run the *Nix* installer as the `nix` user.

Now do the Nix install:

`update-ca-certificates`
`mkdir /nix`
`chown -R nix /nix`
`su - nix`
`bash <(curl `[`https://nixos.org/nix/install`](https://nixos.org/nix/install)`)`
`exit`

*Note*: Yes, we are piping curl into bash. You'll have to trust that the nixos.org website isn't hacked.

Now that *Nix* is installed, the rest must be done as `root`, which the `exit` puts you back into. From here on, you do not need the `nix` user at all.

### Create a Nix Profile

Next up, create a *Nix profile* for the root user, remove the default *nixpkgs* channel, and add the nixos channel.

`. ~nix/.nix-profile/etc/profile.d/nix.sh `
`nix-channel --remove nixpkgs`
`nix-channel --add `` nixos`
`nix-channel --update`

Optional: Install vim for editing
---------------------------------

If you want to use *vim* to edit text instead of *nano*, install it.

    nix-env -i vim_configurable

Install *nixos-install* packages
--------------------------------

Next up, let's actually get the NixOS installation file. Create a configuration file for *Nix* that is mostly empty, set some shell variables, and then install some packages.

`cat <`<EOF >` /root/configuration.nix`
`{ fileSystems."/" = {};`
`  boot.loader.grub.enable = false;`
`} `
`EOF`
`export NIX_PATH=nixpkgs=/root/.nix-defexpr/channels/nixos:nixos=/root/.nix-defexpr/channels/nixos/nixos`
`export NIXOS_CONFIG=/root/configuration.nix`
`nix-env -iA config.system.build.nixos-install -A config.system.build.nixos-option -A config.system.build.nixos-generate-config -f "`<nixos>`"`

Configure NixOS
---------------

Now that you have the NixOS installation packages installed, it's time to build the configuration for your system.

If you already have a configuration file that you saved somewhere else, you can put it in `/mnt/etc/nixos/configuration.nix` directly. If you do so, jump down to [Install NixOS!](/#Install_NixOS! "wikilink")

### Generate configuration files

These will generate your configuration files in `/mnt/etc/nixos/`.

`export NIX_PATH=nixpkgs=/root/.nix-defexpr/channels/nixos:nixos=/root/.nix-defexpr/channels/nixos/nixos`
`nixos-generate-config --root /mnt`

### Fix *hardware-configuration.nix*

The hardware scan fails to find the correct setup for us. As such, replace the `hardware-configuration.nix<code> file with the following:

  imports =
    [ <nixpkgs/nixos/modules/profiles/qemu-guest.nix>
    ];

Note: The only part that changes is the thing in <code><>` brackets.

### Fix Kernel Modules Setting

Make sure in your `configuration.nix`, you have the following line. You can add other kernel modules if you want.

`boot.initrd.availableKernelModules = [ "virtio_net" "virtio_pci" "virtio_blk" "virtio_scsi" "9p" "9pnet_virtio" ];`

The NixOS 2014.12 `qemu-guest` hardware module is missing the `virtio_scsi` module.

### Other Changes

Do other changes. Get your users set up as you want them. Get databases and what not set up. Set up your ports. Anything you care about really.

Install NixOS!
--------------

Whoo! Everything's ready now. Just install nixos with its very own command.

`unset NIXOS_CONFIG`
`nixos-install`

### Set Root Password

You should set the root password here. If you forget, you can use Linode's "Reset Root Password" option in the Rescue tab after finishing the install.

`nixos-install --chroot`
`(chroot)$ passwd`
`Enter new UNIX password: ...`
`                         ...`
`                         ...`
`(chroot)$ exit`

Shut Down Finnix
----------------

The installation is finished. The last steps before booting the system is creating a configuration profile in the Linode Manager:

-   Boot Settings
    -   Kernel: Direct Disk
-   Block Device Assignment
    -   /dev/sda: nixos (or whatever you named it at the beginning)
    -   root device: /dev/sda

Change other configuration options to your preference.

Here's is a sample configuration options:

[<File:linode_configuration_profile.png>](/File:linode_configuration_profile.png "wikilink")

Boot up NixOS
-------------

Now hit *boot* in the *dashboard* tab of the *Linode Manager* and wait for NixOS to boot. You can check progress in the Remote Access Console.

You now should have a fully functional NixOS install on Linode, enjoy!