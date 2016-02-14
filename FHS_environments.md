---
title: FHS environments
permalink: /FHS_environments/
---

There are two packages, `buildFHSChrootEnv` and `buildFHSUserEnv`, which allow you to create and load customizable chroot environments. They build a temporary root with all the packages you specified and several other necessary files and scripts to switch into the environment. The first package is suited for creating global environments which should persist for time longer than one run. It generates several scripts which need root rights to work and uses classic Linux `chroot()` call. The second (more modern) creates your environment in temporary directory (unique each run), and uses Linux namespaces to achieve isolated FHS environments which don't require root. They can be used in various situations when patching some program is too hard to make it work in pure, non-FHS environment.

Android source code
-------------------

Android source tree is a good example when these environments come handy -- it's, as can be imagined, an enormous (~20 GB!) pile of source code together with dynamically-linked binaries and Makefiles/scripts ridden with assumptions about OS directory structure and installed packages. It's very hard to patch and clean this tree of assumptions everywhere, and if done it would be very hard to maintain. Instead with Nix expression like this:

    { pkgs ? import <nixpkgs> {} }:

    let fhs = pkgs.buildFHSUserEnv {
      name = "android-env";
      targetPkgs = pkgs: with pkgs;
        [ git
          gitRepo
          gnupg
          python2
          curl
          procps
          openssl
          gnumake
          nettools
          androidenv.platformTools
          jdk
          schedtool
          utillinux
          m4
          gperf
          perl
          libxml2
          zip
          unzip
          bison
          flex
          lzop
        ];
      multiPkgs = pkgs: with pkgs;
        [ zlib
        ];
      runScript = "bash";
      profile = ''
        export USE_CCACHE=1
        export ANDROID_JAVA_HOME=${pkgs.jdk.home}
      '';
    };
    in pkgs.stdenv.mkDerivation {
      name = "android-env-shell";
      nativeBuildInputs = [ fhs ];
      shellHook = "exec android-env";
    }

you can quickly and conveniently get into an FHS-compatible environment that the tree likes with just `nix-shell`.

Games
-----

`buildFHSUserEnv` is used to allow NixOS users to seamlessly use Steam without any binary patching by just running `steam`. Games distributed as .tar.gz archives can be another example when it is handy. Just figure out needed packages for the game, place `shell.nix` into the game directory and launch the game via `nix-shell`.