---
title: Pidgin with OTR
permalink: /Pidgin_with_OTR/
---

      nixpkgs.config = {
        allowUnfree = true;
        packageOverrides = pkgs: with pkgs; {
          pidgin-with-plugins = pkgs.pidgin-with-plugins.override {
            plugins = [ pidginotr ];
          };
        };
      };