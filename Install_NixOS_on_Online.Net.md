---
title: Install NixOS on Online.Net
permalink: /Install_NixOS_on_Online.Net/
---

This page is a slightly modified version of [the instructions for Linode](/Install_NixOS_on_Linode "wikilink"), and all real credit goes to the authors of that page. I won't bother explaining as much here, so if something is unclear, please refer back to the original page. I've tested this with the Dedibox SC2 and the Dedibox XC, but see no reason for it not to work with all of their configurations.

Start by installing a flavor of Linux (don't care which, since we'll be blowing it away) using the standard Online.net control panel. Make sure the partition layout is as you'd like it, because I'm not including instructions below for changing that.

You don't even need to wait for it to finish booting; click the rescue button on the right and tell it to boot into the ubuntu rescue console. It'll give you a temporary password for the rescue ssh session. Ssh into the rescue image and let's get started!

Preparation
-----------

### Be root

No reason not to be:

    sudo su
    cd

### Change the UI language

All the Online.net images are set to French by default, which I don't want:

    export LANG=POSIX
    export LC_ALL=POSIX

### Check your partitions

Now, make sure your partitions are where you expect them, on /dev/sda.

    fdisk -l /dev/sda

Here's what mine look like on a Dedibox XC:

    /dev/sda1 *          2048     391167    194560  83 Linux
    /dev/sda2          391168 1949523967 974566400  83 Linux
    /dev/sda3      1949523968 1953523711   1999872  82 Linux swap / Solaris

### Format your partitions

This will blast away any existing data on your main disk, so make sure you're okay with that.

    mkfs.ext2 /dev/sda1
    mkfs.ext4 /dev/sda2 -L nixos

    swapoff -a
    mkswap /dev/sda3

### Mount your freshly minted filesystems

and mount the new ones we made:

    mount /dev/sda2 /mnt
    mkdir /mnt/boot
    mount /dev/sda1 /mnt/boot
    swapon /dev/sda3

Nix-flavored stuff
------------------

### Get nix onto the rescue system

We'll need some nix packages to install nix on the target:

    bash <(curl https://nixos.org/nix/install)
    . /root/.nix-profile/etc/profile.d/nix.sh
    nix-channel --remove nixpkgs
    nix-channel --add http://nixos.org/channels/nixos-14.04 nixos
    nix-channel --update

    cat <<EOF > configuration.nix
    { fileSystems."/" = {};
      boot.loader.grub.enable = false;
    }
    EOF

    export NIX_PATH=nixpkgs=/root/.nix-defexpr/channels/nixos:nixos=/root/.nix-defexpr/channels/nixos/nixos
    export NIXOS_CONFIG=/root/configuration.nix
    nix-env -i -A config.system.build.nixos-install -A config.system.build.nixos-option -A config.system.build.nixos-generate-config -f "<nixos>"

### Make configuration for your target system

Generate a default configuration:

    nixos-generate-config --root /mnt

This will generate `/mnt/etc/nixos/configuration.nix` and `/mnt/etc/nixos/hardware-configuration.nix`. Eyeball the latter (nano is preinstalled) to make sure the filesystem config looks reasonable and that it's detected your cores correctly. Then customize the former to your liking. By default it'll use DHCP, which didn't seem to work for me on online.net (please tinker and tell me if you got it working with DHCP though), so make sure to customize the network settings if you don't change anything else.

If you need to figure out your current network settings to fill in the gaps in my settings file, try `cat /etc/resolv.conf` and `cat /etc/network/interfaces`.

Here's a sample config from my box:

    { config, pkgs, ... }:

    {
      imports =
        [ # Include the results of the hardware scan.
          ./hardware-configuration.nix
        ];

      boot.loader.grub = {
        enable  = true;
        version = 2;
        device  = "/dev/sda";
      };

      networking.hostName        = "yourhostname";
      networking.interfaces.eth0 = { ipAddress = "your ip address"; prefixLength = 24; };
      networking.defaultGateway  = "your gateway";
      networking.nameservers     = [ "62.210.16.6" "62.210.16.7" ];

      i18n.defaultLocale = "en_US.UTF-8";

      # environment.systemPackages = with pkgs; [
      #   wget
      # ];

      time.timeZone = "America/New_York";

      services.openssh.enable = true;

      security.sudo.wheelNeedsPassword = false;

      users.extraUsers.youruser =
        { createHome      = true;
          home            = "/home/youruser";
          description     = "your name";
          extraGroups     = [ "wheel" ];
          useDefaultShell = true;
          openssh.authorizedKeys.keys = [
            "<your pubkey here>"
          ];
        };
    }

### Actually install the system

    unset NIXOS_CONFIG
    nixos-install

That should spend some time downloading and copying stuff around, and then should fail without error. After that, tell the web console to reboot back into normal mode and your machine will be beautifully nixified. Except for the logo stuck on the Online.net console page. Let me know if you figure out how to get rid of that!

Troubleshooting
---------------

If `nixos-install` fails to download files from the internet, check that your rescue image's `/etc/resolv.conf`is not a symlink. The issue should be fixed now, but at some point the `nixos-install` chroot process would not copy symlinks appropriately, so things would not resolve in the jail.

See [this commit](https://github.com/NixOS/nixpkgs/commit/6ebe4a6a523bbab3388453ac119ab08e295a7e06) for the fix.