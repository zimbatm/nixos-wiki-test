---
title: Open issues:derivation styles
permalink: /Open_issues:derivation_styles/
---

Different styles can be used to pass dependencies to derivations. There has been lots of discussion about which style is best. This page tries to list advantages and disadvantages. (TODO: add your comments)

General items to be considered:

1.  code duplication (never write something twice)
2.  static checking. If you syntax check all-packages.nix, many missing or ill-typed names are caught.

Note: The derivation styles have names so that we can talk about them more easily.

__TOC__

verbose style
-------------

pro: faster to evaluate.

con: you have to write the name of dependency 3x.

all-packages.nix:

      [...]
      package = import path {
        inherit fetchurl stdenv depA depB; # 1x
      }
      [...]

path/default.nix:

      { stdenv, fetchurl, depA, depB}: # 2x
      stdenv.mkDerivation {
        buildInputs = [depA depB]; # 3x
        [...]
      }

with style
----------

pro: fast to write and easy to maintain for simple packages (Marc Weber)

con: it's said to make evaluation slower

all-packages.nix:

      same as "verbose style"

path/default.nix:

      args: with args;
      stdenv.mkDerivation {
        buildInputs = [depA depB]; # 2x
        [...]
      }

rescuing with style
-------------------

pro: Might make evaluation faster again

con: a. is duplicated

all-packages.nix: same as "verbose style"

path/default.nix:

      a@{...}:
      stdenv.mkDerivation {
        buildInputs = [a.depA a.depB]; #2x
        [...]
      }

get-args-in-file 1
------------------

This was introduced [by Yury G. Kudryashov](http://comments.gmane.org/gmane.linux.distributions.nixos/3753)

pro/con: no duplication in all-packgaes.nix (see 2 above )

all-packages.nix:

      mypkg = makeOverridable (import ../path/to/mypkg) ( pkgs // { cg = getPkgConfig "mypkg"; } );

path/default.nix:

      a:
      let
        inherit (a) stdenv fetchurl other things cg; # 1x
        inherit (a.gtkLibs) gtk glib;
      in
      stdenv.mkDerivation {
        buildInputs = if cg "gtk" false then [ gtk glib ] else []; # 2x
      }

get-args-in-file 2
------------------

This was committed by Eelco Dolstra in SVN r17160.

pro/con: no duplication in all-packages.nix (see 2 )

all-packages.nix:

      foo = callPackage (import ./foo.nix) { };

path/default.nix:

      args: with args;
      or
      {arg1, arg2, ...}:

comments taken from commit -r :17160

-   Use "args: with args;" in the package's function definition.

`   This however obscures the actual expected arguments of a`
`   function, which is very bad.`

-   Use "{ arg1, arg2, ... }:" in the package's function definition

`   (i.e. use the ellipis "..." to allow arbitrary additional`
`   arguments), and then call the function with all of "pkgs" as an`
`   argument.  But this inhibits error detection if you call it with`
`   an misspelled (or obsolete) argument.`

[Mail by Eelco giving more details](http://thread.gmane.org/gmane.linux.distributions.nixos/3753/focus=3764)

string-with-deps-style
----------------------

I'm not sure this style can even be compared against the others. It's defining a customizable builder. There are many examples. Watch out for builderDefsPackage in all-packages.nix to find those examples.

composableDerivation
--------------------

This is similar. It tries to make the default derivation customaziable adding a way to define build options. It automatically passes dependencies or adds configuration options..

Versioned Derivations
---------------------

### versionedDerivation

` xml-library = callPackage ./xml-library/default.nix { };`
` xml-library-2.0 = callPackage ./xml-library/default.nix { version = "2.0"; };`
` xml-library-dev = callPackage ./xml-library/default.nix { version = "dev"; };`

` {stdenv, flex, bison, versionedDerivation, version ? "1.0"}`

` versionedDerivation "xml-library" version {`
`   // merge version specific settings [1]`
`   "dev" = {`
`     name = "xml-library-latest";`
`     src = prefetchgit { url = ..; sha256 = "..."; };`
`     buildInputs = [ autoconf automake libtool];`
`     preConfigure = ''`
`       automake && autoconf`
`     '';`
`     // [3] if you want name (such as buildInputs) to be merged by // use`
`     // mergeAttrsBy = { name = a: b: a // b; };`
`   };`
`   "1.0" = {`
`     name = "xml-library-1.0";`
`     src = fetchurl { url = ..; sha256 = "..."; }`
`   };`
`   "2.0" = {`
`     name = "xml-library-2.0";`
`     src = { url = ..; sha256 = "..."; };`
`   };`
` } {`
`   // shared data [2];`
`   configureFlags = [ ... ];`
`   buildInputs = [ flex bison ];`
`   meta = { ... };`
` }`

Explanation: If version matches "dev" "1.0" or "2.0" those values get merged with the shared data \[2\] - otherwise a nice error message is shown. The merged attr set gets passed to mkDerivation as usual.

The merge is done by mergeAttrsByFuncDefaultsClean (https://github.com/NixOS/nixpkgs/pull/310) having a predefined mergeAttrsBy attr set declaring common merge operations for configureFlags, buildInputs and the like.

### callPackages style

This was introduced by Eelco in [summer 2015](https://github.com/NixOS/nixpkgs/commit/0770a49d8ca9f4639a394eb2bfd302b4d5ee981e); it uses function arguments instead of attribute set merging. Many packages have been converted over to using it.

[Category:Development](/Category:Development "wikilink")