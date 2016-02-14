---
title: Bootstrapping NixOS on ARM
permalink: /Bootstrapping_NixOS_on_ARM/
---

Introduction
------------

Most other installation methods require a pre existing image such as a CD or a system tarball to install the system. This isn’t very helpful if you’re porting NixOS to a new system or otherwise need to bootstrap a system from itself.

To do this, you need the following:

-   The system already running GNU/Linux.
-   A drive to install NixOS to.
-   An Internet connection.
-   Some Unix skills.
-   Patience when compiling.

Optionally a partition to hold temporary files if the drive you’re compiling on and installing to is slow, such as an SD card.

The plan is to install Nix in to the existing operating system on the target system, install NixOS’ installer, then use that to install NixOS on to another drive. This may take a while if you need to compile everything from scratch.

Throughout this you’ll be given commands to run. Don’t run them blindly, read this tutorial fully first so you understand and modify them to suit your needs.

Given that I’m bootstrapping using an i.MX6 ARM board, I’ll note the build times as I go on to give a bit of an idea of how long you may have to wait.

Partitioning
------------

The device I’ll be installing to in this case is an SD card at /dev/mmcblk0. It has the follow partition scheme, including mount locations:

-   /dev/mmcblk0p1 (1GiB, ext4), mounted at /mnt/nixos/boot
-   /dev/mmcblk0p2 (4GiB, swap), used as swap
-   /dev/mmcblk0p3 (24GiB, ext4), mounted at /mnt/nixos
-   /dev/sda3 (50GiB, ext4), mounted at /mnt/nix_tmp

Partitioning, formatting and mounting a disk is all out of scope of this tutorial. There are lots of online guides detailing how to do this already.

Installing Nix
--------------

If you already have Nix or your distribution has a package, you won’t need most of this section but it has important information, so read on!

The first thing you want to do is get Nix, the package manager installed. If you’re on Debian, you’ll need to install these dependencies:

    apt-get install libbz2-dev libsqlite3-dev libcurl4-openssl-dev \
      libdbd-sqlite3-perl libwww-curl-perl

Now, make a directory to download and install Nix to. Inside it, run this:

    wget http://nixos.org/releases/nix/nix-1.8/nix-1.8.tar.xz
    tar -xvf nix-1.8.tar.xz
    cd nix-1.8
    ./configure
    make -j 4 # Change 4 to the number of cores your system has.
    # Wait 3 minutes.
    make install

If you want to run Nix outside of the default system-wide install directory, NixOS will have to recompile a lot of tools when installing.

You must also add Nix builder users, like this:

    groupadd -r nixbld
    for n in $(seq 1 10); do useradd -c "Nix build user $n" \
        -d /var/empty -g nixbld -G nixbld -M -N -r -s "$(which nologin)" \
                nixbld$n; done

You should now have a working Nix package manager installed in to your system to be used. If you’re using a partition for temporary installation and building, you’ll want to bind mount the store to be on that partition, like so:

    mkdir /mnt/nix_tmp/store
    mkdir -p /nix/store
    mount --bind /mnt/nix_tmp/store /nix/store

Now it’s time to configure Nix’s sources. If you want to use channels, write something like this:

    nix-channel --add https://nixos.org/channels/nixpkgs-unstable

But for the purposes of this tutorial, I’ll be using the Git repository. To do this, clone it to a directory and set Nix’s path like this:

    git clone https://github.com/NixOS/nixpkgs.git nixpkgs
    export NIX_PATH="$PWD"

If you already know of Nixpkgs configuration values you’ll need to change to run NixOS, you should put them in ~/.nixpkgs/config.nix and use the same in NixOS later. In this case, I used a custom platform in my config.nix like this:

    pkgs:
    {
      platform = pkgs.platforms.sheevaplug;
    }

Installing Installers
---------------------

It’s time to install the NixOS installer. To do this you need a configuration for NixOS, so make a barebones configuration in /etc/nixos/configuration.nix. It’s important that you place the configuration there as it’ll be copied by the installer. Here’s one similar to mine, make sure to configure it to your needs:

    { pkgs, config, ... }:
    {
      boot = {
        initrd.kernelModules = [];
        kernelParams = []; # Handled by U-Boot.
        kernelModules = [];
        kernelPackages = pkgs.linuxPackages;
        loader = {
          grub.enable = false;
          generationsDir = {
            enable = true;
            copyKernels = true;
          };
        };
      };
      sound.enable = false;
      services = {
        nixosManual.enable = false;
      };
      fileSystems = [
        { mountPoint = "/";
          device = "/dev/mmcblk0p3";
          options = "relatime";
          }
        { mountPoint = "/boot";
          device = "/dev/mmcblk0p1";
          neededForBoot = true;
          }
        ];
      swapDevices = [ { device = "/dev/mmcblk0p2"; } ];
      };
      nixpkgs.config = {
        platform = pkgs.platforms.sheevaplug;
      };
    }

Note how I copied my nixpkgs configuration in to the nixpkgs.config section, you must do this too. It’s time to install the installers. Run something like this:

    export NIXOS_CONFIG=/etc/nixos/configuration.nix
    nix-env -i \
      -A config.system.build.nixos-install \
      -A config.system.build.nixos-option \
      -A config.system.build.nixos-generate-config \
      -j 4 --cores 4 -f "nixpkgs/nixos" # Set 4 to the cores you have.
      # The -f flag points to the nixpkgs repo. Modify it or remove it as needed.
    # Wait 4.5 hours.

The installers are installed! If you just installed Nix you’ll have to set your environment up to use installed programs, like this:

    ln -s /nix/var/nix/profiles/default/ $HOME/.nix-profile
    export NIX_LINK="$HOME/.nix-profile"
    export PATH=$NIX_LINK/bin:$NIX_LINK/sbin:$PATH

Installing NixOS
----------------

It’s time to install NixOS. Although there’s some slight bit of preparation to do, you’re really close. Promise!

The installer will copy your existing Nix installation if possible, and any channels you’re using. If you’re using a cloned repository, you need to copy it to /etc/nixos/nixpkgs in your NixOS installation like this:

    # Change /mnt/nixos to the install's root if needed.
    # Change $PWD to the directory containing nixpkgs if needed.
    mkdir -p /mnt/nixos/etc/nixos
    cp -r $PWD/nixpkgs /mnt/nixos/etc/nixos/nixpkgs

Also if you’re using temporary storage you’ll need to bind its Nix store:

    # Change /mnt/nixos to the install's root if needed.
    mkdir /mnt/nix_tmp/os_store
    mkdir -p /mnt/nixos/nix/store
    mount --bind /mnt/nix_tmp/os_store /mnt/nixos/nix/store

Now run the installer:

    # Change /mnt/nixos to the install's root if needed.
    cp $NIXOS_CONFIG /mnt/nixos/$NIXOS_CONFIG
    nixos-install --root /mnt/nixos \
      -j 4 --cores 4 # Change 4 to your number of cores.
    # Wait 6 hours.

If you have trouble with configuration make sure to modify the configuration.nix in the partition you’re installing to, or copy it again. If you have trouble with packages, make sure to modify the nixpkgs outside of the partition being installed to, then copy it over.

If you’ve been using a temporary partition for building, you’ll have to unmount it and copy the store contents to the final drive:

    umount /mnt/nixos/nix/store
    cp -r /mnt/nix_tmp/os_store/* /mnt/nixos/nix/store

You may have a boot loader installed, but in my case I still had work to do. Luckily, I can get an easy shell inside my new install like this:

    nixos-install --root /mnt/nixos --chroot

You should now have a finished NixOS install!