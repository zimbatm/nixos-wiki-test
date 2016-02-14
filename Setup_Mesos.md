---
title: Setup Mesos
permalink: /Setup_Mesos/
---

This page is not yet complete, you are welcome to add to this page, Thanks!!
============================================================================

This page walks you through the configuration required to enable and run Mesos on NixOS (or generically any Linux distro with Nix package manager installed).

Install Mesos
=============

-   You may install through command line:

` - $ nix-env -i mesos`

-   You may add this to configuration.nix

` - environment.systemPackages = with pkgs; [ mesos zookeeper ];`

-