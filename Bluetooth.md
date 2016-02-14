---
title: Bluetooth
permalink: /Bluetooth/
---

Enabling Bluetooth in NixOS
---------------------------

Enabling Bluetooth is as simple as putting this in your configuration.nix:

    hardware.bluetooth.enable = true;

Bluez 4
-------

This is the default for now (as of August 2013), as KDE 4.10 and Pulseaudio 4.0 do not yet support Bluez 5.

Bluez 5
-------

To use Bluez 5, you need something like this in your Nixpkgs config (in your `~/.nixpkgs/config.nix`):

    pkgs : {
      packageOverrides = pkgs : {
        bluez = pkgs.bluez5;
      }
    }