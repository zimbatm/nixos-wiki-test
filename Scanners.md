---
title: Scanners
permalink: /Scanners/
---

Add SANE firmware
-----------------

Many scanners require firmware blobs which can be downloaded from the website of the scanner or extracted from the drivers they provide. Once you have the appropriate firmware you need to tell SANE where to find it in your configuration.nix:

    hardware.sane.enable = true;
    nixpkgs.config.sane.snapscanFirmware = /firmware/esfw41.bin;
    nixpkgs.config.packageOverrides = pkgs: {
      xsaneGimp = xsane.override { gimpSupport = true; };
    };

Support for HP scanners
-----------------------

    hardware.sane.extraBackends = [ pkgs.hplipWithPlugin ];