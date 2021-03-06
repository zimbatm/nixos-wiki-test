---
title: NixOS related repositories and work
permalink: /NixOS_related_repositories_and_work/
---

For various reasons some work has not been committed to the main trunk repositories. You find a description about some of them here:

Haskell overlay (stable)
------------------------

Let's you install nearly all hackage packages easily. Older and newer gtk2hs supported. [hack-nix](http://haskell.org/haskellwiki/Hack-Nix) also provides a convenient way to setup multiple build environments for a project [nixpkgs-haskell-overlay](http://github.com/MarcWeber/nixpkgs-haskell-overlay)

Ruby overlay (stable)
---------------------

purpose: abstract away tedious task of finding matching versions and packaging ruby packages. (source: RubyGems) Let's you install all packages known by gem. (Contact Marc Weber if you want to try it, you need some more patches). [nixpkgs-ruby-overlay](https://gitorious.org/nixpkgs-ruby-overlay/) Usage example (~/.nixpkgs/config.nix)

`   rubyEnv = `
`     let ro = (pkgs.overlay "ruby");`
`     in ro.rubyEnv19 {`
`         p = {`
`            activesupport = `[ `"="` `"2.3.9"` ](/_"="_"2.3.9"_ "wikilink")`;`
`         };`
`         # note: the gem dependencies of activesupport will be found automatically`
`         names = [ "activesupport" ];`
`     };`

Note: if two packages resolve different versions of the same dependency Nix fails. You can work around it by forcing a version as shown above.

Python overlay (experimental)
-----------------------------

purpose: abstract away tedious task of finding matching versions and packaging python packages (source: PyPi package index) Let's you install all packages known by gem. (Contact Marc Weber if you want to try it, you need some more patches). Open issues: How to find out which package has to be run with python-2.x or python-3 and such. [nixpkgs-python-overlay](https://gitorious.org/nixpkgs-python-overlay/)

nixpkgs-utilities
-----------------

a very small collection of scripts you can run to verify that you don't break too much when comitting. This is work in progress [nixpkgs-dev-utilities](http://gitorious.org/nixpkgs-dev-utilities/). Current goals:

-   verify that everything still evaluates
-   verify that most important things build on your system
-   preview of what will be rebuild if you change something

ocamlPackages
-------------

A small collections of [OCaml](http://caml.inria.fr/)-related packages: [download](http://www.math.unifi.it/~maggesi/ocamlPackages).

Reasons for not committing it to trunk: Proof of concept, still experimenting, trying to find a good way to cope with findlib and other OCaml-specific practices.

nanonixos
---------

<http://viric.name/cgi-bin/nanonixos/> cross compiled nixos by Lluis Batlle i Rossell

guix
----

GNU Guix is a purely functional package manager, and associated free software distribution, for the GNU system based on nix initiated by Ludovic Courtes <http://www.gnu.org/software/guix/>