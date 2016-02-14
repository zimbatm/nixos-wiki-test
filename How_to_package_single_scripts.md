---
title: How to package single scripts
permalink: /How_to_package_single_scripts/
---

I spent about an hour yesterday writing a single script package for nix. I've been using nix for 5 hours or so, but expected to be easier to use for such case.

Initial package
---------------

First up, we'll create a package which simply downloads and copy the script into the output folder (if don't override the phases it will complain about don't recognize the format and later about missing compilation processes). Put the following in a file named script.nix

`{stdenv}:`
`with import <nixpkgs> {};`
`#{stdenv, fetchurl}:`
``
`stdenv.mkDerivation {`
`  name = "trash-0.1";`
``
`  src = fetchurl {`
`    url = https://rawgit.com/chilicuil/learn/master/sh/tools/trash;`
`    sha256 = "0fe78144273d6afac1cef15de4a768ceb7f689a490b3c89110179df61401afae";`
`  };`
``
`  #unpackPhase patchPhase configurePhase buildPhase checkPhase installPhase`
`  #fixupPhase distPhase`
`  phases = [ "unpackPhase" "installPhase" "fixupPhase" "distPhase" ];`
``
`  unpackCmd = ''`
`    stripHash $curSrc`
`    mkdir trash`
`    cp -prd --no-preserve=timestamps $curSrc trash/$strippedName`
`  '';`
``
`  installPhase = ''`
`    mkdir -p $out/bin`
`    chmod +x ../trash/$strippedName`
`    cp -r ../trash/* $out/bin`
`  '';`
``
`  #dontStrip = true;`
`  #dontPatchELF = true;`
`  #dontPatchShebangs = true;`
`}`

Install this by running: nix-env -i -f default.nix

Be aware than for some reason the script must be copied to **$out/bin** not **$out/usr/bin**, nor **$out/other/path** but **$out/bin** otherwise nix won't expose it (it will be installed but not available).