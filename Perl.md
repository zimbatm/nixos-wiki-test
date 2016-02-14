---
title: Perl
permalink: /Perl/
---

How to add something from CPAN to nixpkgs
=========================================

-   Use the nix-generate-from-cpan.pl script (see nixpkgs/maintainers/scripts/) to generate something appropriate. Example usage: nix-generate-from-cpan.pl Devel::REPL
-   After reviewing the result from the previous step and making appropriate modifications, add it to pkgs/top-level/perl-packages.nix. Note that some things use buildPerlPackage while some use buildPerlModule. Also note the mostly-followed naming convention as well as the mostly-followed alphabetical ordering. There are plenty of examples in perl-packages.nix -- use the source, Luke!
-   Build and test.
