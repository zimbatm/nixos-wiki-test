---
title: Network Manager
permalink: /Network_Manager/
---

Installation
------------

### backend: networkmanager

To enable NetworkManager service in NixOS:

`   networking.networkmanager.enable = true;`

### frontend: GTK applet

To use **nm-applet**, you have to install **networkmanagerapplet**.

#### i3-wm + nm-applet

In order for *i3-wm* and *nm-applet* to work together you'll need to do the following in order to get a working setup:

-   Add **gnome3.dconf** to your *configuration.nix*
-   Optional: One way to start *nm-applet* with i3 is to add the following to your *configuration.nix*:

` services.xserver.displayManager.sessionCommands = "${pkgs.networkmanagerapplet}/bin/nm-applet &";`

### frontend: KDE applet

The Network Manager widget for KDE is available in nixpkgs using **kde4.networkmanagement**.

In order to get this working do:

`   environment.systemPackages = with pkgs; [`
`     kde4.plasma-nm`
`   ];`

[Category:Installation](/Category:Installation "wikilink") [Category:Network](/Category:Network "wikilink")