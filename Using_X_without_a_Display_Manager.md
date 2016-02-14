---
title: Using X without a Display Manager
permalink: /Using_X_without_a_Display_Manager/
---

Use xlaunch
-----------

1. Edit configuration.nix appropriately - the following has relevant bits:

      environment.systemPackages = with pkgs; [
        xlaunch
      ];

      security.setuidPrograms = [
        "xlaunch"
      ];

      services.xserver = {
        autorun = false;
        enable = true;
        enableTCP = false;
        exportConfiguration = true;
        layout = "us";
        desktopManager = {
          xterm.enable = false;
          xfce.enable = false;
        };
        # XXX: slim must not be disabled for xlaunch to work
    #    displayManager = {
    #      slim.enable = false;
    #      job.execCmd = "";
    #    };
        windowManager = {
          fluxbox.enable = true;
        };
      };

2. nixos-rebuild as usual:

      # nixos-rebuild switch

3. Possibly reboot

4. Start xlaunch from command line, e.g. for fluxbox:

      $ xlaunch startfluxbox