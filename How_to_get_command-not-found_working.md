---
title: How to get command-not-found working
permalink: /How_to_get_command-not-found_working/
---

Clear channel list and add nixos unstable channel as root: (you can also use <https://nixos.org/channels/nixos-14.04> if you want to stay on the stable channel)

    sudo nix-channel --remove nixos
    sudo nix-channel --add https://nixos.org/channels/nixos-unstable
    sudo nix-channel --update
    sudo nix-channel --update # for luck

Try it out:

    command-not-found xdpyinfo

Mailing list thread: <http://comments.gmane.org/gmane.linux.distributions.nixos/10841>