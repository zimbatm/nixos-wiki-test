---
title: How to package closed-source software
permalink: /How_to_package_closed-source_software/
---

I spent about an hour yesterday writing a package for Thomas Was Alone so I could play it. This is a closed-source game written with the Unity game engine. Here's how I did it, with the hope that others can use this as a guide to packaging other software.

Initial package
---------------

First up, we'll create a package which simply copies the binaries into the output folder. Put the following in a file named default.nix in the same directory as thomaswasalone-linux-1369349552.tar:

`{stdenv}:`
`stdenv.mkDerivation {`
`  name = "thomaswasalone";`
`  src = ./thomaswasalone-linux-1369349552.tar;`
``
`  phases = [ "unpackPhase" "installPhase" ];`
`  installPhase = ''`
`    mkdir -p "$out/opt/thomaswasalone"`
`    cp -r thomasWasAlone thomasWasAlone_Data "$out/opt/thomaswasalone"`
`    chmod +x "$out/opt/thomaswasalone/thomasWasAlone"`
``
`    mkdir "$out/bin"`
`    ln -s "$out/opt/thomaswasalone/thomasWasAlone" "$out/bin/thomasWasAlone"`
`  '';`
`}`

Build this by running: nix-build -E 'with import <nixpkgs> { }; callPackage_i686 ./default.nix { }'

callPackage_i686 is only required if the binary is 32-bit. If it's 64-bit, use callPackage.

If you run ./result/bin/thomasWasAlone now, it'll output something along the lines of "No such file or directory". This is because the dynamic linker isn't referenced correctly in the binary's header.

Patch the linker
----------------

All Linux programs are loaded by a tool known as the dynamic linker, or ld-linux.so. Most binary apps assume this is in /lib or a similar location, which it is not in Nix. Therefore, we have to patch the binary's header to tell the kernel that the dynamic linker is somewhere else. Edit the default.nix:

`{stdenv}:`
`stdenv.mkDerivation {`
`  name = "thomaswasalone";`
`  src = ./thomaswasalone-linux-1369349552.tar;`
``
`  phases = [ "unpackPhase" "installPhase" ];`
`  installPhase = ''`
`    mkdir -p "$out/opt/thomaswasalone"`
`    cp -r thomasWasAlone thomasWasAlone_Data "$out/opt/thomaswasalone"`
`    chmod +x "$out/opt/thomaswasalone/thomasWasAlone"`
``
`    patchelf --set-interpreter $(cat $NIX_GCC/nix-support/dynamic-linker) \`
`      "$out/opt/thomaswasalone/thomasWasAlone"`
``
`    mkdir "$out/bin"`
`    ln -s "$out/opt/thomaswasalone/thomasWasAlone" "$out/bin/thomasWasAlone"`
`  '';`
`}`

Build this again with the same command, and run ./result/bin/thomasWasAlone again. It'll now output something different: "./result/bin/thomasWasAlone: error while loading shared libraries: libGLU.so.1: cannot open shared object file: No such file or directory"

Adding dependencies
-------------------

This is the dull bit. We have to figure out which package each library is contained in, and patch the header of the binary to point to that package. In the case of libGLU.so.1, it's in the "mesa_glu" package. Once you've added that and built the package again, trying to run it will ask for a different package, which you then have to add again. Eventually, you'll end up with something like this:

`{stdenv, mesa_glu, libX11, libXext, libXcursor}:`
`stdenv.mkDerivation {`
`  name = "thomaswasalone";`
`  src = ./thomaswasalone-linux-1369349552.tar;`
``
`  libPath = stdenv.lib.makeLibraryPath [`
`    stdenv.gcc.gcc`
`    mesa_glu`
`    libX11`
`    libXext`
`    libXcursor`
`  ];`
``
`  phases = [ "unpackPhase" "installPhase" ];`
`  installPhase = ''`
`    mkdir -p "$out/opt/thomaswasalone"`
`    cp -r thomasWasAlone thomasWasAlone_Data "$out/opt/thomaswasalone"`
`    chmod +x "$out/opt/thomaswasalone/thomasWasAlone"`
``
`    patchelf --set-interpreter $(cat $NIX_GCC/nix-support/dynamic-linker) \`
`      --set-rpath "$libPath" "$out/opt/thomaswasalone/thomasWasAlone"`
``
`    mkdir "$out/bin"`
`    ln -s "$out/opt/thomaswasalone/thomasWasAlone" "$out/bin/thomasWasAlone"`
`  '';`
`}`

Building and running this will launch the game... but with no sound!

Adding even more dependencies
-----------------------------

In some cases, failing to load some dependencies won't crash the program or be logged precisely. In this case, we have to use strace to find out which .so libraries aren't loaded.

To do this, we can run: strace ./result/bin/thomasWasAlone 2&gt;&1 | grep '^open(".\*\\.so'

This will track all times the program tries to load a .so library and fails. You can filter out most of this. Each library is looked for in multiple locations, and we're only worried if none of them are found. We're also not worried about some - Steamworks, for example, and anything ending in ".dll.so" or ".dll.so.la" here. After looking for a while, we'll find one library which is not successfully opened from anywhere - "libpulse-simple.so.0".

This library is in the "pulseaudio" package, so we'll add that:

`{stdenv, mesa_glu, libX11, libXext, libXcursor, pulseaudio}:`
`stdenv.mkDerivation {`
`  name = "thomaswasalone";`
`  src = ./thomaswasalone-linux-1369349552.tar;`
``
`  libPath = stdenv.lib.makeLibraryPath [`
`    stdenv.gcc.gcc`
`    mesa_glu`
`    libX11`
`    libXext`
`    libXcursor`
`    pulseaudio`
`  ];`
``
`  phases = [ "unpackPhase" "installPhase" ];`
`  installPhase = ''`
`    mkdir -p "$out/opt/thomaswasalone"`
`    cp -r thomasWasAlone thomasWasAlone_Data "$out/opt/thomaswasalone"`
`    chmod +x "$out/opt/thomaswasalone/thomasWasAlone"`
``
`    patchelf --set-interpreter $(cat $NIX_GCC/nix-support/dynamic-linker) \`
`      --set-rpath "$libPath" "$out/opt/thomaswasalone/thomasWasAlone"`
``
`    mkdir "$out/bin"`
`    ln -s "$out/opt/thomaswasalone/thomasWasAlone" "$out/bin/thomasWasAlone"`
`  '';`
`}`

Build this and run, and it'll work perfectly with sound.