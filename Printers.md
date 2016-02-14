---
title: Printers
permalink: /Printers/
---

Printing with CUPS using Gutenprint
-----------------------------------

Install `gutenprint` and configure CUPS to use it as a driver:

        services.printing = {
          enable = true;
          drivers = [ pkgs.gutenprint ];
        };

Look for the model of your printer: `cups-genppd.5.2 -M` (in the `gutenprint` package) and then generate the PPD for that model:

    user@localhost$ cups-genppd.5.2 -v escp2-p50 -p .

Add a printer through the web interface and upload the generated PPD in the section where it asks which driver to use "Or Provide a PPD File:".

Printing with CUPS using HPLIP
------------------------------

If you have an HP printer and Gutenprint does not support it, then the official HP supported HPLIP driver is the next best option.

Before using HPLIP, you should determine whether or not your model of printer requires the proprietary plugin. The easiest way to do that is to look up your model of printer on [HPLIP's supported devices page](http://hplipopensource.com/hplip-web/supported_devices/index.html). If the proprietary plugin is required, it will be noted towards the top of the support page for your device.

Install the HPLIP driver just as you would the Gutenprint Driver. Here we assume your printer model requires the proprietary plugin:

        services.printing = {
          enable = true;
          drivers = [ pkgs.hplipWithPlugin ];
        };

Here is the same without the proprietary plugin:

        services.printing = {
          enable = true;
          drivers = [ pkgs.hplip ];
        };

Since the `hplipWithPlugin` derivation is not cached by Hydra, Nix will require you to compile it yourself nearly every time you update. Because of this, the `hplip` derivation is preferable if you do not require the proprietary plugin.

Now you need to configure your printer in the CUPS web interface. The HPLIP driver does not use the same protocol URI's as other CUPS printer drivers. The easiest way to determine the URI for your device is to use the `hp-setup` utility. The utility can be invoked with the following command:

    user@localhost$ $(nix-build '<nixpkgs>' --no-out-link -A hplip)/bin/hp-setup

That command should launch a graphical interface that allows you to search for HP printers with various search options. Once you've found your printer (Step 2 of 3 in the GUI), copy the Device URI of your printer and use that when specifying the URI in the CUPS web interface.

Other Printers with CUPS
------------------------

Many printers are supported by the Generic PCL driver or the Generic PS driver, which are included with CUPS by default.