---
title: Lambdabot
permalink: /Lambdabot/
---

Installing and running
----------------------

Lambdabot works out of the box with nixpkgs by wrapping the original lambdabot binary and adding paths to GHC_PACKAGE_PATH. That's because code is evaluated at runtime, therefore rpath is not enough in this case. You can install the wrapper as follows:

    $ nix-env '<nixpkgs>' -iA haskellPackages.lambdabotWrapper

Create a sample configuration `lambdabot.rc`:

    irc-connect servername irc.server.org 6667 Lambdabot Lambdabot
    admin + servername:AdminNick
    join servername:#yourchan

Run with `lambdabot -e 'rc lambdabot.rc'`

If the bot does not join `#yourchan` automatically, send the message `@join #yourchan` to your bot from IRC.

Adding more haskell packages
----------------------------

Lambdabot runs with a predefined set of haskell packages that can be used from IRC. Let's say you want to add `blaze-html` in your `~/.lambdabot/State/L.hs` (pointless example as it's probably untrusted), then in your config.nix you can override lambdabotWrapper as follows:

    {
      packageOverrides = pkgs: with pkgs.haskellPackages; {
        haskellPackages = haskellPackages // {
          lambdabotWrapper = lambdabotWrapper.override {
            mueval = muevalWrapper.override {
              additionalPackages = [ blazeHtml lambdabot ];
            };
          };
        };
      };
    }

What we do is overriding the `mueval` input. Note that in the additional packages we also include `lambdabot` itself.