---
title: Xfce
permalink: /Xfce/
---

[Xfce](http://www.xfce.org) is a GTK+ 2 desktop environment.

### Installation

Add *services.xserver.desktopManager.xfce.enable = true;* to /etc/nixos/configuration.nix

### Thunar Volume Support

Add *services.udisks2.enable = true;* to /etc/nixos/configuration.nix

#### Volume mounting does not work

If volume mounting does not work with the XFCE session, enable "Launch GNOME services on startup" in the Advanced tab of the Session and Startup settings panel.

This will fix this kind of error:

`(Thunar:2410): GVFS-RemoteVolumeMonitor-WARNING **: remote volume monitor with dbus name org.gtk.Private.UDisks2VolumeMonitor is not supported`

This command will do the same thing:

`$ xfconf-query -c xfce4-session -p /compat/LaunchGNOME -s true`

Log-out and re-log!

[Category:Services](/Category:Services "wikilink")