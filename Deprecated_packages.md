---
title: Deprecated packages
permalink: /Deprecated_packages/
---

This page contains information about various deprecated packages and their reverse-dependencies.

HAL
---

| Package                                | Maintainer         | Why package needs HAL                                           | Plan                                                            |
|----------------------------------------|--------------------|-----------------------------------------------------------------|-----------------------------------------------------------------|
| [nut](http://www.networkupstools.org/) | Pierron            | Nicolass will try to look at the issue near end of January 2011 |
| [vlc](http://videolan.org/)            | No meta.maintainer |                                                                 |
| virtualbox                             | Sander             | USB hotplug                                                     | Use udisks (search for existing patch)                          |
| pmount                                 | No meta.maintainer |                                                                 | Use udisks --mount                                              |
| Xfce-4.6                               | No meta.maintainer |                                                                 | Switch to 4.8 and remove 4.6                                    |
| gnome-control-center                   | No meta.maintainer |                                                                 | Upgrade                                                         |
| gvfs                                   | No meta.maintainer |                                                                 | Upgrade                                                         |
| gnome-vfs                              | No meta.maintaner  |                                                                 | Disable HAL                                                     |
| pam_usb                               | No meta.maintainer | Hotplug, needs both HAL and pmount                              | Try version from [github](https://github.com/aluzzardi/pam_usb) |

Policykit (not polkit!)
-----------------------

| Package | Maintainer         | Plan               |
|---------|--------------------|--------------------|
| HAL     | No meta.maintainer | Remove (see above) |

Esound
------

Esound is deprecated upstream (GNOME) in favour of pulseaudio.

| Package     | Maintainer                                             | Plan                                                                             |
|-------------|--------------------------------------------------------|----------------------------------------------------------------------------------|
| Xmms        | No meta.maintainers                                    | Remove, there are GTK2 forks                                                     |
| cinelerra   | Marc Weber                                             | Upgrade to CinerellaCV-2.2 or CinerellaHV-4.3, check if esound is still required |
| zapping     | No meta.maintainers; all commits made by Eelco Dolstra | Remove?                                                                          |
| space-orbit | No meta.maintainers (Michael Raskin?)                  | Michael?                                                                         |

Qt3/KDE3
--------

Qt 3.x and KDE 3.x are not supported by Nokia and KDE team. Most packages migrated to KDE 4.x. If we cannot avoid using qt-3.x and/or kde-3.x, look at trinity project.

| Package            | Alternative              | Plan                                       |
|--------------------|--------------------------|--------------------------------------------|
| kde3.k3b           | kde4.k3b                 | Test kde4.k3b, then remove kde3.k3b        |
| kde3.kbasket       | kde4.basket              | Test kde4.basket, then remove kde3.kbasket |
| kde3.kphone        |                          |                                            |
| qcad               |                          |                                            |
| djvulibre (djview) | djview4                  | Remove useQt3 (done in stdenv-updates)     |
| taskjuggler        |                          |                                            |
| valknut            |                          |                                            |
| twinkle            |                          |                                            |
| qucs               |                          |                                            |
| mythtv             |                          |                                            |
| xxdiff/3.2.nix     | xxdiff latest hg version | Upgrade                                    |
| games/six          |                          |                                            |
| qdu                |                          |                                            |

GTK+ 1.x
--------

Glib-1.x fails to build on x86_64, no upstream etc.

### Xmms

There are GTK+-2.x forks. Should be removed.

### Generator

All commits made by Eelco Dolstra. Upstream (almost) dead.