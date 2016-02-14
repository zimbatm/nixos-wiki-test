---
title: Enable Browser Plugins
permalink: /Enable_Browser_Plugins/
---

**Note**: Currently the Google Hangouts Video plugin doesn't work in Chromium. It works in Firefox though.

Per User
--------

It is possible to configure certain features in nixpkgs by creating `~/.nixpkgs/config.nix` and setting the options. See below for details on how to set the equivalent options for the system profile when running NixOS.

To install Firefox and Chromium for a user profile you need to create `~/.nixpkgs/config.nix` and add the following options:

~/.nixpkgs/config.nix:

    {
        allowUnfree = true;

        firefox = {
         enableGoogleTalkPlugin = true;
         enableAdobeFlash = true;
        };

        chromium = {
         enablePepperFlash = true; # Chromium's non-NSAPI alternative to Adobe Flash
         enablePepperPDF = true;
        };

    }

After these options have been set you need to install the "Wrapper" versions of Firefox:

    $ nix-env -iA firefoxWrapper chromium

If you are using a version that does not have a wrapper, you can write one (add to config.nix and install with nix-env -iA firefox-bin-wrapper):

      packageOverrides = pkgs: with pkgs; rec {
        # FF bin with plugins
        firefox-bin-wrapper = wrapFirefox { browser = firefox-bin; };
      };

For NixOS
---------

In NixOS you can configure nixpkgs options by adding a nixpkgs.config section to /etc/nixos/configuration.nix. This has no effect on nix-env commands but does apply to packages installed via the environment.systemPackages list. The options are exactly the same as above.

In order to install Firefox with support for plugins, you need to use the "Wrapper" version. For example, in /etc/nixos/configuration.nix environment.systemPackages this would be firefoxWrapper.

To install Firefox and Chromium with Google Talk and Flash in your system profile you can add the following to your configuration.nix file.

/etc/nixos/configuration.nix:

    ...

    nixpkgs.config = {

        allowUnfree = true;

        firefox = {
         enableGoogleTalkPlugin = true;
         enableAdobeFlash = true;
        };

        chromium = {
         enablePepperFlash = true; # Chromium removed support for Mozilla (NPAPI) plugins so Adobe Flash no longer works
         enablePepperPDF = true;
        };

      };

    environment.systemPackages = [ pkgs.firefoxWrapper pkgs.chromium ];
    ...

then run

    nixos-rebuild switch

Java
----

In case of JRE you need to perform some manual download to comply with its license (Oracle JRE is used, currently). These steps are described in the output when you try to install Firefox. Use the option "jre = true;" in the appropriate configuration file as described above.

Enable GPU support
------------------

For intel:

    services.xserver.vaapiDrivers = [ pkgs.vaapiIntel ];

Gnome Shell extensions
----------------------

Gnome Shell extensions can be enabled with `enableGnomeExtensions = true`. It is a mozilla plugin, therefore it does not work currently with Chromium.

[Category:Configuration](/Category:Configuration "wikilink")