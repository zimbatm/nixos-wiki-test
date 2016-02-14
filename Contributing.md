---
title: Contributing
permalink: /Contributing/
---

Contributing to this wiki
-------------------------

Make sure your articles are linked from the [Main Page](/Main_Page "wikilink") otherwise your page might be considered spam and deleted

Contribute to nixpkgs
---------------------

A more elaborate guide can be found here [Contributing to nixpkgs](/Contributing_to_nixpkgs "wikilink").

### Getting started

See the official documentation which describes how the Nix expression language works so that you can update existing packages and add new ones.

-   [Nix manual](http://hydra.nixos.org/job/nix/trunk/tarball/latest/download-by-type/doc/manual)
-   [Nixpkgs manual](http://hydra.nixos.org/job/nixpkgs/trunk/tarball/latest/download-by-type/doc/manual), in particular [Coding conventions](http://hydra.nixos.org/job/nixpkgs/trunk/tarball/latest/download-by-type/doc/manual#chap-conventions)
-   [NixOS manual](http://hydra.nixos.org/job/nixos/trunk/manual/latest/download)

### I have a patch

Thanks. Open a [pull request](/pull_request "wikilink") on github.

If your change breaks things don't scream if someone reverts it. It happens. The best thing to do is to fix your patch. Try to check first if it will cause a lot of rebuilds and add a note to your pull request if you have already tested everything.

### What meta attributes are important

-   -   meta.platforms\* will tell hydra to build binaries for specific platform targets. By default hydra doesn't build anything.

<!-- -->

-   -   meta.maintainers\* will tell hydra what emails to send when a build fails

<!-- -->

-   -   meta.license\* will tell hydra how to handle package based on license type

Example:

    meta = with stdenv.lib; {
      description = "Munin is a networked resource monitoring tool that can help analyze resource trends";
      homepage = http://munin-monitoring.org/;
      license = licenses.gpl2;
      maintainers = [ maintainers.iElectric ];
      platforms = platforms.linux;
    };

### Check if there are any evaluation errors with your distribution

    nix-instantiate --eval-only --strict --xml -A <packagename>

### How to verify that you didn't break a dependency or the evaluation by accident ?

To find out which packages are effected by your change:

    $ nix-env -f . -qaP \* --drv-path | sort > l1
    (...modify package...)
    $ nix-env -f . -qaP \* --drv-path | sort > l2
    $ changed=$(diff l1 l2 | awk '{print $2}' | sort | uniq)

    # This list can be used to create a release.nix for testing, or you can build all the relevant packages:
    $ nix-build $(for i in $changed; do echo -A $i; done)

    #!/bin/sh
    set -e -x

    export NIXPKGS_ALL=${NIXPKGS_ALL:-/etc/nixos/nixpkgs}

    # evaluate all derivations found in all-packages.nix:
    nix-env -qa '*' --show-trace -P --out-path -f $NIXPKGS_ALL/pkgs/top-level/all-packages.nix

    # evaluate all derivations found in release.nix before starting the build:
    # nix-env -qa '*' $NIXPKGS_ALL/pkgs/top-level/release.nix
    # nix-instantiate --readonly-mode $NIXPKGS_ALL/pkgs/top-level/release.nix --show-trace

    # build a release tarball which contains the most important packages (?)
    nix-build -A tarball $NIXPKGS_ALL/pkgs/top-level/release.nix --show-trace

TODO: explain what is done exactly

TODO: see if this is useful: <https://github.com/NixOS/nixpkgs/blob/master/maintainers/scripts/rebuild-amount.sh>

The nixpkgs-dev-utilities referenced on [ Nix(OS) related repositories and work ](/_Nix(OS)_related_repositories_and_work_ "wikilink") tries to implement this for your convinience

### Merging git pull requests

hitting the "merge" button on github is fine, however doing

`   curl 'patch url' | git am; git push`

is finer, because history will be cleaner.

### Contributing to the nixpkgs manual

To build the manual, you need Nix installed on your system (no need for NixOS). To install Nix, follow the instructions at

<https://nixos.org/nix/download.html>

When you have Nix on your system, in the root directory of the project (i.e., \`nixpkgs\`), run:

    nix-build nixos/release.nix -A manual.x86_64-linux

When this command successfully finishes, it will tell you where the manual got generated.

Contribute to planet.nixos.org
------------------------------

if you havea blog and you do want to contribute to planet.nixos.org then use github.com's inline editor and add yourself to this list:

<https://github.com/NixOS/nixos-org-configurations/blob/master/nixos-org/planet-feeds.nix>

and create a pull-request.

[Category:Contribution](/Category:Contribution "wikilink") [Category:Development](/Category:Development "wikilink") [Category:Hacking](/Category:Hacking "wikilink")