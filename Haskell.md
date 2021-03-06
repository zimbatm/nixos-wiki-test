---
title: Haskell
permalink: /Haskell/
---

The goal of this page is to explain how to use Haskell packages in a Nix/NixOS-based system, and how to contribute Haskell packages to the Nixpkgs collection.

There's a different approach to dealing with Haskell packages that's described on a separate page: [nixpkgs-haskell-overlay](/nixpkgs-haskell-overlay "wikilink"), also known as "Hack-nix".

Getting started
---------------

If you just want to get a running Haskell environment on NixOS quickly, a good way to get started is to install the current Haskell Platform:

` $ nix-env -iA nixos.pkgs.haskellPlatform.ghc`

If all you want is to use the xmonad window manager, then put the following options into your `/etc/nixos/configuration.nix`:

      services.xserver.windowManager.xmonad.enable = true;     # installs xmonad and makes it available
      services.xserver.windowManager.xmonad.enableContribAndExtras = true; # makes xmonad-contrib and xmonad-extras available
      services.xserver.windowManager.default       = "xmonad"; # sets it as default
      services.xserver.desktopManager.default      = "none";   # the plain xmonad experience

In both cases, though, it is recommended to read on in order to learn more about how Haskell packages are organized in Nixpkgs.

Using Haskell packages
----------------------

You get the best Haskell experience in Nix if you use pre-packaged Haskell packages that are available in the Nixpkgs collection. Nixpkgs provides many packages, and each for several versions of GHC. You can select whether you want profiling-enabled versions of these packages or not.

There are two main options how you can install Haskell packages from Nixpkgs on your Nix system:

-   You can **directly install** Haskell packages into one of your profiles. The GHC binaries are then wrappers that are trained to detect libraries that you have installed in this way automatically. This is the simplest way. There are two disadvantages to this solution:
    1.  using `cabal` to install packages that aren't in Nixpkgs is slightly tricky in this setting (because it's harder to train `cabal` to automatically pick up Nixpkgs-installed libraries than it is to train GHC);
    2.  if you have *really many* packages installed, then the automatic detection is somewhat slow, and as it happens on each invocation of `ghc` or `ghci`, it can be somewhat slow.

<!-- -->

-   You can add a **customized GHC environment** to your `~/.nixpkgs/config.nix`. In this scenario, the installed GHC does *not* automatically pick up Haskell libraries to your profile, but *only* the packages listed in your configuration. On the other hand, `cabal` can be used normally and there is no slowdown. Furthermore, it is easy to build several different GHC configurations in this way that use a very specific set of available packages.

Both options are described below in the more detail.

### Obtaining a list of Haskell packages

The best way to get an overview of Haskell packages is to issue the following command:

` $ nix-env -qaP \* | grep haskellPackages | less`

This will yield a long list of packages that looks somewhat as follows:

      nixpkgs.haskellPackages_ghc6104.httpConduit                       haskell-http-conduit-ghc6.10.4-1.4.1.3
      nixpkgs.haskellPackages_ghc6123.httpConduit                       haskell-http-conduit-ghc6.12.3-1.4.1.3
      nixpkgs.haskellPackages_ghc704_no_profiling.httpConduit           haskell-http-conduit-ghc7.0.4-1.4.1.3
      nixpkgs.haskellPackages_ghc704_profiling.httpConduit              haskell-http-conduit-ghc7.0.4-1.4.1.3-profiling
      nixpkgs.haskellPackages_ghc741.httpConduit                        haskell-http-conduit-ghc7.4.1-1.4.1.3
      nixpkgs.haskellPackages_ghc742.httpConduit                        haskell-http-conduit-ghc7.4.1.20120508-1.4.1.3
      nixpkgs.haskellPackages_ghc6104.httpDate                          haskell-http-date-ghc6.10.4-0.0.2
      nixpkgs.haskellPackages_ghc6123.httpDate                          haskell-http-date-ghc6.12.3-0.0.2
      nixpkgs.haskellPackages_ghc704_no_profiling.httpDate              haskell-http-date-ghc7.0.4-0.0.2
      nixpkgs.haskellPackages_ghc704_profiling.httpDate                 haskell-http-date-ghc7.0.4-0.0.2-profiling
      nixpkgs.haskellPackages_ghc741.httpDate                           haskell-http-date-ghc7.4.1-0.0.2
      nixpkgs.haskellPackages_ghc742.httpDate                           haskell-http-date-ghc7.4.1.20120508-0.0.2

Each of these lines lists first an **attribute path** describing the path to the Nix expression defining that particular package, followed by the **name** of the particular derivation. As you can see, each package is available in several versions, for different compiler versions. The name additionally contains the version of the package. For most Haskell libraries, only a single version is available in Nixpkgs, but for some packages, there are several.

Both the attribute paths and the names can be used to install packages from this list, using `nix-env -iA` or `nix-env -i`, respectively. However, using the attribute path is *recommended*. The reason is that when using the attribute path, you'll usually get the *preferred* version of the package for the compiler version you select (in cases where several versions are available), whereas when using the name, you'll get the *latest* version of the package available, even if it is not going to work with the selected GHC version. Note that when you install packages, you have to explicitly select the compiler version you want to use. If you use the attribute path, you can use `haskellPackages` (without `_ghc` suffix) as a synonym for what is considered to be the *current* version of GHC in Nixpkgs (usually the GHC version of the latest Haskell Platform release).

There is a **naming convention** for Haskell packages. The final component of the attribute path is usually a CamelCase variant of the Hackage package name. The name of the derivation starts with the Hackage package name prefixed with `haskell-`, except for packages that only provide an application where the `haskell-` prefix is dropped. So if you understand the naming convention, you can try to install a package without first looking at the list.

Packages available in Nixpkgs are also listed on Hackage itself, in the Distributions field of the package page.

If you want to see the sources that define all these attributes, have a look at the file `pkgs/top-level/haskell-packages.nix` in the Nixpkgs hierarchy.

### Profiling

If you use Haskell for development, you might be interested in having profiling versions available for all libraries. This is easy to achieve by editing or creating `~/.nixpkgs/config.nix` and placing the following content in the file:

` pkgs : {`
`   cabal.libraryProfiling = true;`
` }`

If the file already exists, then drop the first and last line and just add the option to the list of other options you already declare.

### Direct installation

As explained above, if you install a wrapped version of GHC, then it will be able to automatically pick up additional libraries you install from Nixpkgs. The easiest way to obtain such a wrapped GHC is by installing the Haskell Platform, by saying:

` $ nix-env -iA nixos.pkgs.haskellPlatform`

for the default version of the platform (and GHC), or

` $ nix-env -iA nixos.pkgs.haskellPackages_ghc6123.haskellPlatform`

for a specific version of GHC and the corresponding platform.

If you either do not want to install the platform or want to use a recent version of GHC for which no platform has been released yet, then install the wrapped GHC itself, by saying:

` $ nix-env -iA nixos.pkgs.haskellPackages.ghc`

or

` $ nix-env -iA nixos.pkgs.haskellPackages_ghc742.ghc`

for a specific version of GHC. This will install a derivation with a name ending in `-wrapper`.

Subsequently installed libraries of the matching GHC version will be detected automatically.

**Do not** install the GHC with attribute path `ghcPlain` (with a name not having the `-wrapper` part), because then you will not be able to use libraries. If you accidentally installed this version, remove it from your profile using `nix-env -e ghc` and start again as explained above.

#### Using several versions of GHC at once

It is not possible to install multiple versions of GHC into the same profile. However, you can create a separate profile for each version of GHC, for instance:

` $ nix-env -p ~/ghc-6.10.4 -iA nixpkgs.haskellPackages_ghc6104.ghc`
` $ nix-env -p ~/ghc-7.6.2  -iA nixpkgs.haskellPackages_ghc762.ghc`

And add both profiles to your command search path:

`export PATH="$HOME/ghc-7.6.2/bin:$HOME/ghc-6.10.4/bin:$PATH"`

Unversioned commands like `ghc` will refer to GHC 7.6.2. If you want to run an older version, you have to invoke it explicitly (e.g., `ghc-6.10.4`).

Obviously, libraries are GHC-version-specific, so you might have to install the libraries you want to use in each of these versions several times.

#### Using **cabal** in the direct installation scenario

\[To be written.\]

Cabal can depend on a number of binaries at run time, which are not installed by default when cabal is, including **ar**, **happy**, **alex**, and **haddock**. In particular **ar** the library archiver is needed for the **cabal install** command to work properly. **ar** can be found in the standard environment and there are few methods to get **cabal install** working properly with it.

-   Install the standard environment into the profile that contains cabal. This is probably prefered for a profile whose main purpose is for cabal usage.

` nix-env -f "`<nixpkgs>`" -iA pkgs.stdenv`

-   Alternatively you can use a [custom environment](/Howto_develop_software_on_nixos#BBB_manual_cloning_of_an_environment_not_using_the_package_manager "wikilink") for cabal can be created an environment that loads the **ar** through the standard environment and load it with **load-env-$(env-name)**.

#### Keeping Haskell packages up-to-date

\[To be written.\]

### Customized GHC environment

As indicated above, using a wrapped GHC can be somewhat uncomfortable at times, because it behaves a bit differently from the original program. Furthermore, while the easy detection of installed libraries is useful in some settings, there are other scenarios where you want full an reliable control over the packages available. For these purposes, there exists a function `ghcWithPackages` within the `haskellPackages` attribute sets that enables you to build a GHC with a clearly specified set of packages in its global package database. The easiest way to use this function is via `~/.nixpkgs/config.nix` or `/etc/nixos/configuration.nix`.

#### Local use via myEnvFun

I find it handy to use `pkgs.myEnvFun` described in [Howto develop software on nixos](/Howto_develop_software_on_nixos#Obtaining_an_Environment_from_a_Custom_Definition "wikilink") to describe my development environment in its entirety, using a separate environment for each project.

an example `my-dev-env.nix`:

      let
        pkgs = import <nixpkgs> {};
        hsEnv = pkgs.haskellPackages.ghcWithPackages (hsPkgs : ([
          hsPkgs.HaRe
          hsPkgs.hlint
          hsPkgs.hdevtools
          hsPkgs.hasktags] ++
          # Include the deps of our project to make them available for tools:
          (hsPkgs.callPackage ./my-haskell-project.nix {}).propagatedNativeBuildInputs;
      in
        pkgs.myEnvFun {
          name = "my-haskell-project";
          buildInputs = with pkgs; [
            # development tools as fits your needs
            binutils
            coreutils
            hsEnv
            ];
          extraCmds = ''
            $(grep export ${hsEnv.outPath}/bin/ghc)
          '';
          }

Since tools (such as `hdevtools`) that rely on the ghc-api to do haskell language manipulation are sensitive to nix-wrapping of ghc, we need the `extraCmds` line to ensure the proper ghc-wrapping environment variables are set inside the dev-environment shell.

Use by issuing:

` $ nix-build my-dev-env.nix`
` $ ./result/bin/load-env-my-haskell-project`

#### Local use via Nixpkgs config

Here is a simple yet complete example of how to use `ghcWithPackages` in `~/.nixpkgs/config.nix`:

` pkgs : {`
`   packageOverrides = self : rec {`
`     hsEnv = self.haskellPackages.ghcWithPackages (self : [`
`         self.haskellPlatform`
`         # add more packages here`
`     ]);`
`   };`
` }`

If you know the Nix attribute name of a particular package (you only need the last component), you can add it to the list to have it (and its dependencies) installed for the selected version of GHC. As always, by using `haskellPackages.ghcWithPackages`, we refer to the current version of GHC. You can explicitly select another version of GHC, too. For example, to use GHC 7.4.2, you would say `haskellPackages_ghc742.ghcWithPackages`.

The configuration above defines a *new* attribute called `hsEnv` that you can subsequently install into an environment by saying

` $ nix-env -iA nixos.pkgs.hsEnv`

It is trivial to keep this environment up-to-date. You can just reinstall the package from time to time. If you want to add or remove packages, you edit the packages list and reinstall.

Note that you can easily declare several Haskell environments in your configuration file, and switch between them, or even better, install them to different profiles and switch between the profiles.

#### System-wide use via NixOS config

The instructions for the user-specific approach are valid for the system-wide approach as well. You simply add the `ghcWithPackages` call to the `environment.systemPackages` attribute in your `/etc/nixos/configuration.nix`:

      {config, pkgs, ...} :

      {
        # other configuration options

        environment.systemPackages = [
          # other system packages
          (pkgs.haskellPackages.ghcWithPackages (self : [
            self.haskellPlatform
            # add more Haskell packages here
          ]))
        ];
      }

#### Overriding package preferences

\[This whole part still has to be filled with (more) content.\]

Generating new Haskell packages and contributing them to Nixpkgs
----------------------------------------------------------------

Expressions for individual packages are all auto-generated using the `cabal2nix` and `hackage4nix` tools that are explained below. Additional documentation about the structure of partially generated files is provided below, but keep in mind that changes will usually be made in combination with these tools.

### Individual packages

#### Package expressions

Most Haskell package expressions are located at `development/libraries/haskell`, but a few live elsewhere. There are plans to move them all to a common location. All individual packages are composed in `top-level/haskell-packages.nix`, so you can look at that file to see all the paths of Haskell packages in the Nixpkgs collection.

Each individual expression is a call to a Haskell-specific function `cabal.mkDerivation` that is explained below. Each individual expression is usually generated by a call to `cabal2nix` that is also explained below.

Here is a typical Nix expression for a Cabal package:

` { cabal, deepseq, hashable }:`
` `
` cabal.mkDerivation (self: {`
`   pname = "unordered-containers";`
`   version = "0.2.2.1";`
`   sha256 = "0ny8w7xw0ch3yp0fnskzygz61b72ln5s5ccsdlcqpp29cvfar6zy";`
`   buildDepends = [ deepseq hashable ];`
`   meta = {`
`     homepage = "`[`https://github.com/tibbe/unordered-containers`](https://github.com/tibbe/unordered-containers)`";`
`     description = "Efficient hashing-based container types";`
`     license = self.stdenv.lib.licenses.bsd3;`
`     platforms = self.ghc.meta.platforms;`
`   };`
` })`

This package (as nearly all packages) is generated by a call to `cabal2nix`. You rarely have to (or should) adapt the output of `cabal2nix`, but let's nevertheless discuss the important non-meta arguments to `cabal.mkDerivation`:

`pname`
The package name

`version`
The version of the package

`sha256`
The sha256 checksum of the Hackage tarball (the location of the package is automatically assumed to be on Hackage)

`src`
Usually not required. For non-Hackage packages, a `src` attribute can be defined, much like for other packages.

`buildDepends`
Build-time Haskell dependencies of the package.

`buildTools`
Additional Haskell build tools (such as Alex or Happy) required to build the package.

`extraLibraries`
Dependencies on non-Haskell libraries.

`pkgconfigDepends`
Dependencies on non-Haskell and pkgconfig-based libraries.

`isLibrary`
Whether the package defines a library. Defaults to `true`.

`isExecutable`
Whether the package defines an executable. Defaults to `false`. Note that Cabal packages can define both a library and several executables.

`noHaddock`
If set to `true`, do not generate Haddock documentation for this package. Can be used to prevent Haddock-specific errors in a few cases.

`jailbreak`
If set to `true`, strip version bounds from the Cabal file. Can be used to make packages build that would otherwise have too strict dependency bounds.

The last two flags described above can sometimes be used if the auto-generated package description won't immediately work.

#### The Cabal builder

The builder lives in `build-support/cabal/default.nix` and handles building of nearly all Cabal-based Haskell packages in nixpkgs. It is designed in such a way that most actual package expressions look simple and are close to the Cabal file they are based on.

It defines all the attributes described above.

If changes to the Cabal builder are necessary, they should be made very careful, because they will usually trigger rebuilds of all Haskell packages.

#### cabal2nix

The `cabal2nix` tool can be used to generate Nix expressions (such as the one for `unordered-containers` shown above automatically. The `cabal2nix` package is itself on Hackage and in nixpkgs, so it can be installed via Nix. Call `cabal2nix --help` to get an overview on how to use it. Typically, invocation is quite easy. For example, the package description shown above can be produced by saying

` cabal2nix cabal://unordered-containers`

Note that `cabal2nix` will download the package from Hackage to obtain its checksum, unless you already have the tarball in your Nix store.

The tool `cabal2nix` is used to autogenerate all Haskell package in nixpkgs currently. The generation process analyzes all packages that are currently there, and detects certain adaptations to the Nix expressions (such as the presence of a `noHaddock` flag, or maintainer settings), and makes sure to preserve them upon regeneration. However, if a package requires more severe changes to the `cabal2nix` output, then the tool itself should be adapted.

### GHC

\[To be written.\]

### Composing Haskell packages

\[To be written.\] (This is about the contents of `haskell-defaults.nix` and `haskell-packages.nix`.)

### Maintaining your own set of additional Haskell packages

\[To be extended.\]

If you use `cabal2nix` to generate expressions for new Haskell packages, you can either add them to the nixpkgs collection immediately, or place them separately. If you want to do the latter, then `~/.nixpkgs/haskell` or a similar location is suitable. In order to make such expressions available in your personal nixpkgs collection, you can use the nixpkgs configuration file, which should then have approximately the following structure:

` {`
`   packageOverrides = pkgs : {`
`     myHaskellPackages = pkgs.recurseIntoAttrs (pkgs.haskellPackages.override {`
`       extraPrefs = hsPkgs : {`
`         haskellpackage = hsPkgs.callPackage ./haskell/haskellpackage.nix {};`
`       };`
`     });`
`   };`
` }`

Here, `haskellPackage` refers to a local Haskell package. More packages could be added, of course.

Instead of `pkgs.haskellPackages.override`, you can select another set of `haskellPackages` from `all-packages.nix`, for the GHC version of preference. The `pkgs.recurseIntoAttrs` call is optional, but it ensures that you can view your own new Haskell packages in `nix-env -q` calls.

### Haskell defaults and package collections

\[To be written.\]

### What is the wrapper about?

ghc has to know about its core and additional libraries. While the unwrapped haskellPackages.ghc.ghc ghc only knows about the core packages (try its ghc-pkg list command) the wrapped does no longer, but it knows about additional libraries installed by looking at $PATH env. That the wrapped ghc-pkg does not list core packages can be considered a bug - but its not annoying enough - so nobody has spent the time to fix it. Just have a look at the wrapper to understand how it works. Are there alternatives? - using GHC_PACKAGE_PATH - using a local package file - ...

how to get the derivations of either one?

` nix-build -A haskellPackages[version].ghc # the wrapper`
` nix-build -A haskellPackages[version].ghc.ghc # the wrapped ghc`

[Category:Development](/Category:Development "wikilink") [Category:Haskell](/Category:Haskell "wikilink") [Category:Nixpkgs](/Category:Nixpkgs "wikilink")