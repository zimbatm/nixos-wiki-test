---
title: Nix Modifying Packages
permalink: /Nix_Modifying_Packages/
---

Sometimes we must modify an existing Nix expression to install the software we need. For example, suppose a Nix expression exists for "Ruby 3.0.0p0", but we need the "Ruby 3.0.0p360" revision. How can we get this newer revision of Ruby?

We have a few options to get a one-off Nix package when using Nix expressions.

-   Fork the NixPkgs project and modify the package's Nix expression in your fork, then, optionally, push this modification back to the project.
-   Create a Nix expression which overrides existing Nix packages.

Fork and Modify NixPkgs
-----------------------

If we want to modify an existing package and we think the existing package should no longer be used, we can modify the package and push this update to the central NixPkgs repository. For a detailed walk-through, see the [Contributing to NixPkgs](/Contributing_to_nixpkgs "wikilink") Wiki page, but for now, let's look at the overview.

### Fork NixPkgs

Log into your GitHub account, or create one, then click the "Fork" button on the central [GitHub repository](https://github.com/NixOS/nixpkgs%7CNixPkgs).

Clone your fork of the project to your computer to modify it and test your changes.

`   $ git clone `[`https://github.com/your_username/nixpkgs.git`](https://github.com/your_username/nixpkgs.git)` ~/my-fork`

### Modifying a Forked Package

Now you can modify a package. Open the `~/my-fork/nixpkgs/pkgs/top-level/all-packages.nix` file and find the name of the package you want to modify from the set of attributes. Consider Filezilla, for example, which is defined like this: `filezilla = callPackage ../applications/networking/ftp/filezilla { };`. To modify the Filezilla package, modify the `../applications/networking/ftp/filezilla/default.nix` file, as `default.nix` is the default file name used by Nix if not specified.

### Using Your Fork of NixPkgs

After modifying a package's Nix expression, we need to test it. When we normally use Nix commands, such as the `nix-env -i` command, the default NixPkgs installation is used. We can, however, specify a different NixPkgs installation for Nix commands to use.

`   ### Install from default NixPkgs:`
`   $ nix-env -i firefox`

To specify a different NixPkgs installation, we need to add it to Nix's search path, which is used to find Nix expressions. To do this, modify the `NIX_PATH` environment variable to add a path alias, which looks like "my-nixpkgs=/path/to/fork/nixpkgs". With this path alias defined, we can use it to tell Nix to use our NixPkgs fork. To use the path alias in Nix commands, place it inside angle brackets, like "`<nowiki><my-nixpkgs></nowiki>`", when using Nix commands.

`   ### Install from our NixPkgs fork: (yet to be tested by author)`
`   $ export NIX_PATH=my-nixpkgs=/home/$USER/my-fork:$NIX_PATH`
`   $ nix-env -i firefox -f '<my-nixpkgs/default.nix>'`
`   $ nix-env -i firefox -f '`<my-nixpkgs>`'`
`   # OR, if angle brackets aren't working...`
`   $ nix-env -i firefox -f ~/my-fork/default.nix`

If we have problems, we can individually execute each phase of the Nix build process by using the `nix-shell` command.

`   $ nix-shell ~/my-fork/nixpkgs -A firefox`
`   $ nix-shell '`<my-nixpkgs>`' -A firefox # (yet to be tested by author)`
`   [nix-shell]$ unpackPhase`
`   [nix-shell]$ patchPhase`
`   [nix-shell]$ configurePhase`
`   [nix-shell]$ buildPhase`
`   ...`

For more information about debugging a Nix package, see the [Debugging a Nix Package](/Debugging_a_Nix_Package "wikilink") and [Create and Debug Nix Packages](/Create_and_debug_nix_packages "wikilink") wiki pages.

After debugging the package, we can test the results of the build by using the `nix-build` command or directly install it using the `nix-env -i` command.

Overriding Existing Packages
----------------------------

If we don't want to create a fork of NixPkgs or we believe our modifications will not be useful to other people, we can instead "patch" our default NixPkgs installation.

When Nix evaluates the NixPkgs Nix expression, it will also evaluate the file located at the `~/.nixpkgs/config.nix` path and combine it with the default NixPkgs expression. If we want to use a different file, specify a file path in the `NIXPKGS_CONFIG` environment variable and Nix will instead use that one.

### Creating a `config.nix` File

What does Nix expect the `config.nix` file to look like? To modify existing packages, this file must contain an attribute named `packageOverrides`, which will be called as a function and given the original NixPkgs list as a parameter. Here are some example `config.nix` files.

Override a package's arguments:

`   {`
`     packageOverrides = pkgs: rec {`
`       # Make "xbmc" use the "python26" package,`
`       #   instead of NixPkgs default python version.`
`       xbmc = pkgs.xbmc.override {`
`         python = pkgs.python26;`
`       };`
`     };`
`   }`

Override a package's attributes:

`   {`
`     packageOverrides = pkgs: rec {`
`       # Override the "mu" package's "postInstall" phase to move some files.`
`       mu = pkgs.stdenv.lib.overrideDerivation pkgs.mu (oldAttrs : {`
`         postInstall = ''`
`           mv $out/share/emacs/site-lisp/mu4e/* $out/share/emacs/site-lisp/`
`         '';`
`       });`
`       `
`       # Create a new package, "mySed", which is identical to an`
`       #   existing package, "gnused", but uses a different version.`
`       mySed = pkgs.stdenv.lib.overrideDerivation pkgs.gnused (oldAttrs: {`
`         name = "sed-4.2.2-pre";`
`         src = fetchurl {`
`           url = `[`ftp://alpha.gnu.org/gnu/sed/sed-4.2.2-pre.tar.bz2`](ftp://alpha.gnu.org/gnu/sed/sed-4.2.2-pre.tar.bz2)`;`
`           sha256 = "11nq06d131y4wmf3drm0yk502d2xc6n5qy82cg88rb9nqd2lj41k";`
`         };`
`         patches = [];`
`       });`
`     };`
`   }`

Overriding Haskell library packages is described in great detail in [A Journey into our brand-new Haskell infrastructure: Part II](http://lists.science.uu.nl/pipermail/nix-dev/2015-January/015608.html).

<https://nixos.org/wiki/Using_Oracle_JDK_instead_of_Open_JDK>

For a closer look on exactly how Nix loads this config file, look at the [`all-packages.nix: Line 54`](https://github.com/NixOS/nixpkgs/blob/master/pkgs/top-level/all-packages.nix#L54) to see how it is retrieved and saved to the `config` attribute. To see how this file is used to "patch" the existing packages, see [`all-packages.nix: Line 108`](https://github.com/NixOS/nixpkgs/blob/master/pkgs/top-level/all-packages.nix#L108), which uses its `packageOverrides` attribute to create the `pkgs` attribute, which is used for the rest of the NixPkgs.

Managing private Nix packages outside the Nixpkgs tree
------------------------------------------------------

Here detailed examples and explanations are given on how to manage a single private package or a collection of private packages: <http://sandervanderburg.blogspot.com/2014/07/managing-private-nix-packages-outside.html>. It is an easier way to get started as no forking of nixpkgs is needed.