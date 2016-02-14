---
title: Using the ZSH SHELL
permalink: /Using_the_ZSH_SHELL/
---

problem description
-------------------

i love to use zsh but on nixos there is a issue with the PATH variable so that once you changed from bash to zsh commands like: ssh mybox "ls -la" won't work.

my solution:
------------

in a bash shell do:

`   export | grep '^PATH='`
`   PATH=/var/setuid-wrappers:/home/joachim/.nix-profile/bin:/home/joachim/.nix-profile/sbin:/home/joachim/.nix-profile/lib/kde4/libexec:/nix/var/nix/profiles/default/bin:/nix/var/nix/profiles/default/sbin:/nix/var/nix/profiles/default/lib/kde4/libexec:/var/run/current-system/sw/bin:/var/run/current-system/sw/sbin:/var/run/current-system/sw/lib/kde4/libexec`

then put it into your ~/.zshenv file like this:

`   echo "export $(export | grep '^PATH=')" > ~/.zshenv`

solution
--------

write this into your configuration.nix

` programs.zsh.enable = true;`

this will automatically install zsh into your system and provide a /etc/zshenv which is sourced every zsh invocation. this way you will always have the most recent PATH entries and suffer no more! thanks eelco.