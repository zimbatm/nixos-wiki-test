---
title: Talk:Steam
permalink: /Talk:Steam/
---

Composing FHS-compatible chroot environments with Nix (or deploying Steam in NixOS)
-----------------------------------------------------------------------------------

I think the Steam page should contain something like this:


    # http://sandervanderburg.blogspot.de/2013/09/composing-fhs-compatible-chroot.html
    #
    # Something that confused me when setting this up
    # was that direct rendering for 32bit applications
    # is not enabled by default when running a 64bit
    # nixos system. This caused steam to complain and
    # made every game I tried fail to start. To enable
    # it I had to add
    # "services.xserver.driSupport32Bit = true;"
    # to configuration.nix. Just FYI for others who
    # might be confused by this.

    ############# starting #############

    nix-env -i steam
    nix-env -i steam-chrootenv

    sudo init-steam-chrootenv
    sudo mount-steam-chrootenv
    load-steam-chrootenv

    steam

    ############# stopping #############
    sudo umount-steam-chrootenv
    sudo destroy-steam-chrootenv

Just edited your draft for the commands.

No need to `sudo load-steam-chrootenv` as it does it itself

And a bonus thing is that it does sudo as the current user inside the chroot.

Then, no need anymore for: `su -s /bin/bash username` :).

[Samueldr](/User:Samueldr "wikilink") ([talk](/User_talk:Samueldr "wikilink")) 02:48, 27 May 2015 (CEST)

Moved
-----

Moved this into to the main page