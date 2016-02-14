---
title: Awesome Window Manager
permalink: /Awesome_Window_Manager/
---

Installation
------------

Add this line in `/etc/nixos/configuration.nix`:

`   services.xserver.windowManager.awesome.enable = true;`

Run `nixos-rebuild switch`. Then in a virtual terminal, restart the display manager with:

`   systemctl restart display-manager`

Configuration
-------------

Copy the bundled configuration file into your home directory:

`   $ mkdir -p ~/.config/awesome`
`   $ cp /run/current-system/sw/etc/xdg/awesome/rc.lua ~/.config/awesome/`

Customize it to fit your needs. See [Awesome's homepage for details](http://awesome.naquadah.org/).

[Category:Services](/Category:Services "wikilink") [Category:Desktop Environments](/Category:Desktop_Environments "wikilink")