---
title: Solve GConf errors when running GNOME applications
permalink: /Solve_GConf_errors_when_running_GNOME_applications/
---

The result is that applications that use GConf -- like GnuCash -- will report tons of error messages at start-up. To remedy those errors, the GConf daemon must be told the path to the application-specific ${out}/etc/gconf/gconf.xml.defaults/ directory, and the DBus daemon must be told how to start gconfd whenever an application needs it. To accomplish that, follow these steps:

1. Add the following line to **~/.gconf.path**:

`   xml:readwrite:$(HOME)/.gconf`
`   xml:readonly:$(HOME)/.nix-profile/etc/gconf/gconf.xml.defaults/`
`   xml:readonly:/root/.nix-profile/etc/gconf/gconf.xml.defaults/`
`   xml:readonly:/run/current-system/sw/etc/gconf/gconf.xml.defaults`

2. Add the following line to **/etc/nixos/configuration.nix**:

`   services.dbus.packages = [ pkgs.gnome.GConf ];`
`   environment.pathsToLink = [ "/etc/gconf" ];`

3. Make the configuration active:



4. It may also be necessary to run:



<!-- -->


in case there already is an active gconfd, but those beasts typically terminate on their own after a few moments of inactivity.

