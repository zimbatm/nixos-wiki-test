---
title: Lenovo Thinkpad x200
permalink: /Lenovo_Thinkpad_x200/
---

Lenovo ThinkPad X200
====================

Overview
--------

| Component | Hardware       |
|-----------|----------------|
| Graphics  | Intel Mobile 4 |
| Wireless  | Intel 5100 AGN |
| Suspend   | 3.1.5          |
||

Manual configuration
--------------------

### System Management Application Program Interface

On the ThinkPad X200 some system information is made available through the System Management Application Program Interface (SMAPI) instead of the Advanced Configuration and Power Interface (ACPI) or Advanced Power Management (APM). This mostly concernes more detailed battery and improved accellerometer information.

For this to work you need to add tp_smapi to the boot.extraModulePackages option and also specify the tp_smapi module in boot.kernelModules in /etc/nixos/configuration.nix.

`boot.kernelModules = [ ... "tp_smapi" ];`
`boot.extraModulePackages = [ ... config.boot.kernelPackages.tp_smapi ];`

Then rebuild your system using the nixos-rebuild command.

You can find additional information about the tp_smapi module in the [ThinkPad Wiki](http://www.thinkwiki.org/wiki/Tp_smapi).

Tips & Tricks
-------------

### Disable speaker

Add this to your /etc/nixos/configuration.nix.

`boot.blacklistedKernelModules = [ "pcspkr" ];`

[Category:Installation](/Category:Installation "wikilink") [Category:Hardware](/Category:Hardware "wikilink") [Category:Laptops](/Category:Laptops "wikilink")