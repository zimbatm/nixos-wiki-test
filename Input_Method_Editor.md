---
title: Input Method Editor
permalink: /Input_Method_Editor/
---

Input Method Editors (IME) allow input in foreign languages, often used for Asian languages like Chinese, Japanese, and Korean. This article explains how to use common IMEs on NixOS.

Fcitx
-----

[Fcitx](https://fcitx-im.org/wiki/Fcitx) has recently become a popular IME on Linux. It is increasingly the IME of choice for many users. It allows input in many languages, most commonly used for Chinese, Japanese, and Korean. Fcitx is available on Nix starting with the 14.04 release, and with plugins available starting with the 14.11 release.

### Installation

#### On NixOS

Fcitx can be installed by adding it to your /etc/nixos/configuration.nix file. If you need to edit Fcitx settings through a GUI, install the fcitx-configtool package as well.

    { config, pkgs, ... }:

    {
      imports =
        [ # Include the results of the hardware scan.
          ./hardware-configuration.nix
        ];
      (...)

      environment.systemPackages = with pkgs; [
      (...)
      fcitx
      fcitx-configtool
      (...)
      ];
    }

#### With Nix

Fcitx can be installed as a normal user using nix-env.

` nix-env -iA nixos.pkgs.fcitx`
` nix-env -iA nixos.pkgs.fcitx-configtool`

### Installation with Plugins

Fcitx is available with plugins on the unstable and 14.11 releases.

#### On NixOS

It can be installed by adding it to your /etc/nixos/configuration.nix. Below is an example of enabling the anthy plugin for Japanese input.

    { config, pkgs, ... }:

    {
      imports =
        [ # Include the results of the hardware scan.
          ./hardware-configuration.nix
        ];
      (...)

      environment.systemPackages = with pkgs; [
      (...)
      (fcitx-with-plugins.override { plugins = [ anthy ]; })
      fcitx-configtool
      (...)
      ];
    }

### Setup

The [Configuration](https://wiki.archlinux.org/index.php/fcitx#Configuration) section of the Arch Linux wiki on Fcitx is very comprehensive and works on NixOS as well.

### Available Plugins

Fcitx plugins are available starting with the 14.11 release.

#### Anthy

Anthy allows Japanese input and conversion to kanji. For instance, when using Anthy, typing 'nihon' will input the characters にほん, and then pressing SPACE will turn it in to 日本.

### Additional Resources

-   [Fcitx on Arch Linux Wiki](https://wiki.archlinux.org/index.php/fcitx)
