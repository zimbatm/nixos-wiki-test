---
title: Talk:Haskell
permalink: /Talk:Haskell/
---

I'm running nix under debian rather than running nixos. I found this command did not work for me:

$ nix-env -iA nixos.pkgs.haskellPlatform

Instead, I managed to dig up

$ nix-env -iA nixpkgs.haskellPackages.haskellPlatform

Which is now at least looks like it's downloading things. Does the documentation need updating?

Using cabal in the direct installation scenario
-----------------------------------------------

This section seems out of date. I add a section like this to my default.nix file...

haskellPackages.cabal.mkDerivation (self: {

` pname = "mypacakge";`
` version = "0.1.0.0";`
` src = "./.";`
` isLibrary = false;`
` isExecutable = true;`
` buildTools = with haskellPackages; [cabalInstall];`
` buildDepends = with haskellPackages; [ comonad distributive];`
` meta = {`
`   license = self.stdenv.lib.licenses.gpl3;`
`   platforms = self.ghc.meta.platforms;`
` };`

The buildTools line ensures cabal and supporting binaries are there.