---
title: Category:Edit Needed
permalink: /Category:Edit_Needed/
---


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
    sudo load-steam-chrootenv

    su - /bin/bash username
    steam

    ############# stopping #############

    sudo umount-steam-chrootenv
    sudo destroy-steam-chrootenv