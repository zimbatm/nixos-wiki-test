---
title: KDE
permalink: /KDE/
---

WikiPedia Definition
--------------------

> ''KDE *(/ˌkeɪdiːˈiː/)* is an international free software community producing an integrated set of cross-platform applications designed to run on Linux, FreeBSD, Solaris, Microsoft Windows, and OS X systems. It is known for its Plasma Desktop, a desktop environment provided as the default working environment on many Linux distributions, such as openSUSE, Mageia and Kubuntu and is default desktop environment on PC-BSD, a BSD operating system.''

KDE on Nix
----------

KDE and Nix are both cross-platform. Add hydra and Disnix in to the mix and you have the means to create local, private and secure application deployment network that is cross-platform. Add to the fact that KDE and Nix(OS) are very versatile in regards to options and configurations that you give the user, administrator or developer a lot of leeway to develop and deploy stable production environments.

The code behind KDE
-------------------

The reason why KDE is cross-platform compatible is because it bases it self on the C++ GUI framework QT. QT was developed by Trolltech and has since been sold and re-sold again to different commercial outfits. But it has not deterred the development of the KDE suite and it still serves as one of the best examples of what QT is capable of doing.

How to install KDE 4
--------------------

KDE 4 is older, but stable and well-tested (both within NixOS and by the KDE developers).

*1. Open a terminal and two text editor windows/tabs*

*2. Open `/etc/nixos/configurations.nix` and `/etc/nixos/hardware-configurations.nix`*

*3. Either find or create a `services.xserver` entry, like so*

` services.xserver = {`
`   displayManager.kdm.enable = true;`
`   desktopManager.kde4.enable = true;`
` };`

(services.xserver)
Rather than writing *services.xserver* at the beginning of every variable we make it into an array to make the editing and scope of the subject easier by abstraction.

(services.xserver.)displayManager.kdm.enable
uses KDM as the default login screen

(services.xserver.)desktopManager.kde4.enable
uses KDE as the default desktop environment

*4. You can install KDE applications individually, or install the entire suite of KDE software. Find or create a `environment.systemPackages` entry, like so:*

`environment.systemPackages = [

 ];`

*To install everything, you can copy this list into the `environment.systemPackages` entry:*

`
 # KDE packages
 pkgs.kde4.kdemultimedia pkgs.kde4.kdegraphics pkgs.kde4.kdeutils pkgs.kde4.applications pkgs.kde4.kdegames pkgs.kde4.kdeedu pkgs.kde4.kdebindings pkgs.kde4.kdeaccessibility pkgs.kde4.kde_baseapps pkgs.kde4.kactivities pkgs.kde4.kdeadmin pkgs.kde4.kdeartwork pkgs.kde4.kde_base_artwork pkgs.kde4.kdenetwork pkgs.kde4.kdepim pkgs.kde4.kdepimlibs pkgs.kde4.kdeplasma_addons pkgs.kde4.kdesdk pkgs.kde4.kdetoys pkgs.kde4.kde_wallpapers pkgs.kde4.kdewebdev pkgs.kde4.oxygen_icons pkgs.kde4.kdebase_workspace pkgs.kde4.kdelibs pkgs.kde4.kdevelop pkgs.kde4.kdevplatform
`

How to install KDE 5
--------------------

KDE 5 is the latest release. As with all new releases, it is not as stable as KDE 4, but it is suitable for regular use.

### Desktop Environment

To install KDE 5, add this entry to your in your `/etc/nixos/configuration.nix`: `
  services.xserver.desktopManager.kde5.enable = true;
` Installing KDE 4 and KDE 5 at the same time is not recommended because some files have overlapping names. It is safe to switch back to KDE 4 even after installing KDE 5, but you should disable KDE 5 when you re-enable KDE 4.

There is not yet a specific display manager for KDE 5, but any display manager is suitable.

### Applications

For KDE 5, the software has been separated into three releases. The KDE Frameworks are the libraries that support the desktop and applications. KDE Plasma is the desktop environment. KDE Applications are the actual applications. Two sets of KDE applications are maintained:

`pkgs.kdeApps_latest`
the latest available version, for testing

`pkgs.kdeApps_stable`
an older stable version, for daily use.

When `kdeApps_latest` has been tested, it moves to `kdeApps_stable`. At any time, they may be the same version, depending on the speed of stabilization.

By default, only a few essential applications are installed. Any other applications can be installed by adding them to `environment.systemPackages`. To install all the available KDE applications from the `kdeApps_stable` set, add this entry: `
  # If you have an existing environment.systemPackages entry, you should append
  # this entry instead, using the ++ operator.

  environment.systemPackages =
    (builtins.attrValues
      (removeAttrs kdeApps_stable
        [ "deepOverride" "override" "overrideDerivation"
          "recurseForDerivations" "scope" ]));
`

Although the Plasma desktop and Frameworks have been ported to Qt 5, many of the applications are still Qt 4 based. To have a uniform look-and-feel between Qt 4 and Qt 5 applications, run `qtconfig` and select the Breeze GUI Style. For a uniform appearance with GTK applications, the Orion theme is installed by default; you can select this theme in any GTK theme chooser. (GTK theme selection will be integrated into Plasma 5.2)

### Testing

Please report any bugs on the Nixpkgs GitHub [issue tracker](https://github.com/NixOS/nixpkgs/issues).

Newer versions of Plasma and Frameworks are sometimes available as `plasma5_latest` and `kf5_latest`. To help test these versions, add these package overrides to your `/etc/nixos/configuration.nix`: `
  nixpkgs.config = {
    packageOverrides = super: let self = super.pkgs; in {
      plasma5_stable = self.plasma5_latest;
      kdeApps_stable = self.kdeApps_latest;
    };
  };
`