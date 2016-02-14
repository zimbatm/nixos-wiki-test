---
title: CrossCompiling
permalink: /CrossCompiling/
---

Motivation
----------

1.  Viric wanted to cross-build uboot for the sheevaplug, in order to debug it.
2.  Nicolas Pierron wanted the whole relationships between packages in nixpkgs to be usable also for cross-compilation.

The stdenv wrapper
------------------

The [makeStdenvCross](https://github.com/NixOS/nixpkgs/blob/5ba24cc8ea7c3979a174142393d155bbc32167cf/pkgs/stdenv/adapters.nix#L116) wrapper brings cross-compilation capabilities to nixpkgs. The attribute set resulting from a stdenv.mkDerivation call returns the usual result coming from a usual native build, with two additional attributes:

-   crossDrv - the derivation cross-built
-   nativeDrv - the derivation built in for the build system (a usual native build).

Thus, these calls equivalently build the native derivation for 'bison' (mentioning the 'comtrend.nix' above):

`nix-build -A bison default.nix`
`nix-build -A bison comtrend.nix`
`nix-build -A bison.nativeDrv comtrend.nix`

If we want to cross-build bison for the arm target described above, we can run:

`nix-build -A bison.crossDrv comtrend.nix`

It needs some help from the user, defining for each stdenv.mkDerivation which is native and which is for the cross system:

-   nativeBuildInputs: derivations built for the build system (native builds), needed for the actual cross-build.
-   buildInputs: derivations built for the host-system (to be run in the target), needed for the actual cross-build.
-   propagatedBuildInputs: in addition to being a dependency of the current built, this derivation built for the host-system will be an input for any derivation requiring the actual host derivation being built.
-   propagatedNativeBuildInputs: in addition to being a dependency of the current built, this derivation built for the build-system (native build) will be an input for any derivation requiring the actual host derivation being built.

For usual derivations in the native build, buildInputs and nativeBuildInputs get merged and built for the native system, and analogous for propagatedBuildInputs and propagatedNativeBuildInputs.

**TODO**: These variables should be renamed. Attributes ending with "\*buildInputs" should be renamed to "\*hostInputs" and attributes ending with "\*nativeBuildInputs" should be renamed to "\*buildInputs". This renaming is waiting for an automated tool that will will do the renaming, so pending updates can be merged appropriately.

### Cross-compilation modifications

The builder PATH will contain the gcc cross-compiler with cross-libc. The gcc-cross-wrapper has a setup-hook that modifies the configureFlags, so the calls to the autotools configure script are attempted for a cross build. Also, the *environment variables* for the derivation builder will define **$crossConfig** to the GNU Configuration triplet set in the crossSystem nixpkgs parameter; [cmake](https://github.com/NixOS/nixpkgs/blob/5ef6745be33b2d5bf18ba072bb27a0c5a3d22889/pkgs/development/tools/build-managers/cmake/setup-hook.sh#L54) uses this, for example

If you want to check for cross-compilation in the Nix expression, just check stdenv.cross; e.g. when cross-compiling for Windows, stdenv.cross.libc == "msvcrt".

### Bootstrap of the compiler for cross-compilation

The main nix packages expression has a parameter setting the information for the cross-compilation target. When set, this enables cross-building:

`... , crossSystem ? null, ...`

gccCrossStageFinal is used for all of stdenv's cross-compilation and is built as follows:

-   Build binutils for the target with the native stdenv, crossSystem, and noSysDirs (empty NATIVE_LIB_DIRS) ([binutilsCross](https://github.com/NixOS/nixpkgs/blob/5ef6745be33b2d5bf18ba072bb27a0c5a3d22889/pkgs/top-level/all-packages.nix#L3780))
-   Build a bare-metal gcc, gccCrossStageStatic, using the binutils built (no libc++)
-   Build libcCross (glibc, uclibc, mingw-64, etc.) with the bare-metal gcc and the binutils
-   Build a normal gcc + g++ with the libc just built (gcc_realCross)
-   Wrap the last gcc + g++ with the proper libc [gccCrossStageFinal](https://github.com/NixOS/nixpkgs/blob/5ef6745be33b2d5bf18ba072bb27a0c5a3d22889/pkgs/top-level/all-packages.nix#L2661) (from gcc_realCross)

The gcc's are built using makeGCCCrossWrapper which properly wraps the cross-gcc, cross-binutils, and cross-libc.

This is used by all-packages.nix as `stdenv = stdenvCross = makeStdenvCross defaultStdenv crossSystem binutilsCross gccCrossStageFinal`.

### Cross-compiling in practice

To build a cross-compiled derivation you can do something like the following:

` let`
`   crossSystem = ...;`
`   crossPkgs = import all-packages.nix {system = "x86_64-linux"; inherit crossSystem};`
` in`
`   crossPkgs.stdenv.mkDerivation {`
`     ...`
`   }`

*crossSystem* should be an attribute set with information about the target. For example, we can use this 'comtrend.nix':

`import ./default.nix   # The root nixpkgs default.nix`
`{`
`  crossSystem = {`
`    config = "mips-unknown-linux";`
`    bigEndian = true;`
`    arch = "mips";`
`    float = "soft";`
`    withTLS = true;`
`    libc = "uclibc";`
`    platform = {`
`      name = "comtrend";`
`      kernelMajor = "2.6";`
`      kernelBaseConfig = "bcm63xx_defconfig";`
`      kernelHeadersBaseConfig = "bcm63xx_defconfig";`
`      uboot = null;`
`      kernelArch = "mips";`
`      kernelAutoModules = false;`
`      kernelTarget = "vmlinux.bin";`
`    };`
`    openssl.system = "linux-generic32";`
`    gcc = {`
`      arch = "mips32";`
`    };`
`  };`
`}`

The options available are mostly undocumented; generally you have to check the relevant nix expressions involved in the build you want. Some platforms are defined in nixpkgs already; check all-packages.nix, platform.nix, and [Hydra's tests](https://github.com/NixOS/nixpkgs/blob/5ef6745be33b2d5bf18ba072bb27a0c5a3d22889/pkgs/top-level/release-cross.nix).

For a complete (but outdated) example you can check Viric's [configurations directory](https://nixos.org/websvn/nix/configurations/trunk/misc/viric/cross/).

### What Hydra tests

Out-of-date; see [the build job](http://hydra.nixos.org/jobset/nixpkgs/cross-trunk).

Packages

-   xorg.xeyes
-   bison
-   uboot (this derivation is actually meant for a sheevaplug u-boot)

GNU Configurations

-   armv5tel-unknown-linux-gnueabi
-   armv5tel-unknown-linux-uclibceabi
-   armv5tel-unknown-linux-uclibceabi
-   mips-unknown-linux

Solved problems
---------------

-   gcc 4.4.2 cannot be bootstrapped, because building the bare-metal (without libc or os) first gcc fails. This does not happen in 4.4.1 or 4.4.3(svn) \[SOLVED in nixpkgs\]
-   For uclibc, gcc 4.3.4 needed a patch on libmudflap running a the host CPP instead of the target, on "configure". \[SOLVED in nixpkgs\]

[Category:CrossCompilation](/Category:CrossCompilation "wikilink") [Category:Development](/Category:Development "wikilink")