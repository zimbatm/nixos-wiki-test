---
title: User:Jaeol Tips
permalink: /User:Jaeol/Tips/
---

### Write a config and generate iso or virtualbox image

    # example config
    import <nixpkgs/nixos> { configuration = {

      imports = [
          <nixpkgs/nixos/modules/virtualisation/virtualbox-image.nix>
          <nixpkgs/nixos/modules/profiles/headless.nix>
          <nixpkgs/nixos/modules/profiles/minimal.nix>
          <nixpkgs/nixos/modules/installer/cd-dvd/channel.nix>
        ];

      # FIXME: UUID detection is currently broken
      boot.loader.grub.fsIdentifier = "provided";

      networking.firewall.enable = false;
      services.lshd.enable = true;

      # Enable sudo without password
      security.sudo.enable = true;
      security.sudo.wheelNeedsPassword = false;

      users.extraUsers.joelmo =
        { isNormalUser = true;
          extraGroups = [
            "wheel"
            "vboxsf" # Allow mounting of shared folders.
            "users"
          ];
          group = "users";
          password = "zxc";
          uid = 1000;
        };
    };}

Generate this with `nix-build virtualbox.nix -A config.system.build.virtualBoxImage`