---
title: Kiberpipa sprint 2014 topics wiki backup
permalink: /Kiberpipa_sprint_2014_topics_wiki_backup/
---

NixOS sprint topics
===================

Please add your topics and/or indicate what you are interested to work on.

Participants
------------

-   chaoflow, Florian Friesd orf <flo@chaoflow.net>
-   nbp / pierron, Nicolas B. Pierron
-   ED
-   vcunat, Vladimír Čunát <vcunat@gmail.com>
-   flosse <mail@markus-kohlhase.de>
-   qknight, joachim schiele <js@lastlog.de>
-   Domen Kozar

Topics
------

### Nixtest - testing for nixpkgs

Sprinters: - chaoflow

We have per-package tests during checkPhase and installCheckPhase, and NixOS tests using qemu. Eelco Dolstra suggested [tests for nixpkgs](http://thread.gmane.org/gmane.linux.distributions.nixos/13465/focus=13469).

To test (python-packaging) in nixpkgs, I propose [nixtest](https://github.com/chaoflow/nixtest/blob/master/nixtest.py#L94), and test library and [tests in nixpkgs](https://github.com/chaoflow/nixpkgs/tree/python/tests), would like to discuss with you and possibly develop further.

Key features:

-   Rerun failed tests from command line as your user, i.e. not via nix-build. This greatly eases debugging, not to speak of making it actually fun. The [nixpkgs testing lib](https://github.com/chaoflow/nixpkgs/blob/python/tests/lib/default.nix#L120) creates a wrapper that creates and runs the test environment.
-   Python as test language.
-   [plumbum](http://plumbum.readthedocs.org/en/latest/index.html#user-guide) to construct cli calls.
-   [click](http://click.pocoo.org/) for a powerful cli of the test runner itself
-   Use [ipdb](https://github.com/gotcha/ipdb) to debug your tests, a debugger based on [ipython](http://ipython.org/) featuring tab completion among many other nice things

Missing:

-   log messages in xml format suitable for hydra, currently only logged to stdout via python’s logging library.
-   hydra jobset (within nixpkgs release.nix)

### Wheel-based python packaging

Sprinters: - chaoflow

Wheel-based python packaging

Key features:

-   python wheels are not meant to be installed into pro files
-   instead python.site is used to create sites meant to be installed into profiles. Scripts/entry-points, i.e. stuff in bin/, can be created selectively
-   python.tool is used to create only scripts to be installed into a profile without potentially colliding libraries

For more info see <https://github.com/chaoflow/nixpkgs/tree/pytho04a68d8577acbceb88bdf51b1231a9dbdead7003n/pkgs/development/pytho04a68d8577acbceb88bdf51b1231a9dbdead7003n-wheels>

Missing: - per package testing - generation of test/runtime/extra requirements from wheels metadata - discussion of the overall approach

### Support passwords in Nix & NixOS

Sprinters: - nbp

Currently persons who are making modules might not be aware that any password option would appear in a readable format for all users. This is a sad state as the only source of information of such limitation is the documentation.

We should make this state obvious by adding the “types.password”, which would annotate any strings with the right of the user session. Then “nix” should prevent the files from ever being readable by the user of the same group. This should be applied to both the derivation file and the output of it.

### Different sources of packages

Sprinters: - nbp (maybe?)

Currently Nix use mirrors which are furnish by Hydra to deliver packages. Hydra and its mirrors have been a central point of failure. Other distribution are providing multiple mirrors to answer this solution. Nix is capable of using different source, but the current model is too centralized. Hydra solves 2 issues, the security aspect (Can I trust the way you are compiling packages), and the transport aspect (Can you transfer me this package). This makes Hydra a central point of failure for security and for reliability.

We should change that by adding 2 things; add GPG based authenticity of Nar signatures; add Torrent based transport for nar files. Note that the 2 things are independent from each others.

This would be useful for distributing the charge of trust (Nar signature) to the authors of the packages, while getting a package from the user of the package (Nar file).

\[See also <https://github.com/NixOS/nix/issues/8> - ED\]

### Intensional store model (by Mathnerd314, who won’t be at the sprint)

Sprinters: - Nobody yet

See <https://github.com/NixOS/nix/issues/296#issuecomment-50241878> and <http://comments.gmane.org/gmane.linux.distributions.nixos/13624> / <http://comments.gmane.org/gmane.linux.distributions.nixos/13635>. This subsumes “different sources of packages”, by allowing multiple “builders” (like derivations but more general) to each produce the same output “tokens” (like derivation outputs but more specific). Authenticity is ensured by GPG signatures of every token-output pair. Say remote server R builds token T, producing binary package B. R will use its GPG key to sign the pair (T,B), giving the statement “A holder of the private key of R says they built token T and it produced build B”. Both T and B are identified by cryptographic hashes. Now say I want to install token Q, which depends on the package R built. Local NixOS L, by means that are not in the model, turns this request for Q into a builder derivation which can be summarized as “fetchtorrent <B-hash>”. L runs the builder, downloading B. So now the Nix store has B, and L’s signatures have two entries for B: one from R mapping T to B, and another from L that maps the token for “fetchtorrent B” to B. The transport problem is solved naturally by letting it be part of the Nix model, and trust is solved as well since the user can revoke trust from R and then Nix will automatically search for another instance B’ of T and rewrite all of the hashes B -&gt; B’.

### Update systemd to 216

Sprinters: - ED

Systemd 216 is just out and has lots of new features compared to 212. We probably want this for NixOS 14.10.

### Reduce NixOS closure sizes (“multiple-output” branch)

Sprinters: - ED - vcunat

NixOS is getting a bit fat, which is especially bad for cloud and container deployments. For instance, an almost minimal NixOS container is currently 672 MiB. This can be reduced a lot. Mostly involves getting the multiple-outputs branch into shape (so that build-only dependencies like header files can be eliminated from the system closure).

### Packing Prosody as a service

Sprinters: - flosse

Prosody is a great XMPP-Server written in lua.

<https://github.com/flosse/nixpkgs/tree/prosody>

### Get rid of builderDefsPackage

Sprinters: None

### Support touchscreen

Sprinters: - flosse

To calibrate touchscreen monitors you can use xinput_calibrator.

<https://github.com/flosse/nixpkgs/tree/xinput_calibrator> <https://github.com/NixOS/nixpkgs/pull/3772>

nix-env -i xinput_calibrator

usage: run `xinput_calibrator`, calibrate your screen and copy the output to `services.xserver.conf=`

standup
=======

sunday
------

-   python issues (fixing builds) & and python docu
-   multiple deployment (header vs libs)
-   node npm, npm2nix, node documentation_
-   containers & docker on nixos
-   packaging speech recoginition
-   arduino stuff on nixos (cross compiler)
-   installing nixos + learning nixos
-   gitlab packgaging (ruby)
-   redmine
-   systemd stuff (resolved as nscd replacement), documentation
-   unit tests for fedora using nix
-   nixops cleanup

documentation of nix|hydra
==========================

we need a ‘manual’ link in the top level design like <http://nixos.org/>, in comparison <http://nixos.org/nix/> an <http://nixos.org/hydra> are missing this link, yet they have a docbook documnetation needs fix here: <https://github.com/NixOS/nixos-homepage>

hydra documentation
===================

installation
------------

git checkout edit nixos /etc/nixos/configuration.nix imports hydra-module.nix enable hydra system service postgresql db - <https://github.com/NixOS/nixos-org-configurations/blob/master/delft/lucifer.nix> - reverse proxy httpd; hydra uses perl::catalyst - setup hydra account in postsql, become root, <http://nixos.org/hydra/>, run createuser -S -D -R -P hydra createdb -O hydra hydra su hydra cat ~/.pgpass chmod 0600 ~/.pgpass hydra-create-user sander –role admin –password foo nixos-rebuild switch

how to use hydra
----------------

webbrowser, localhost 1. create project 2. create a jobset for a project, can be multiple 3. jobs = rec {..} build 4. see libiff example, from githuc.om/sandervbburg/ 5a. in there look for release.nix and ‘nix-build release.nix -A tarball’ nix-env -i /nix/store/foo nix-env -f release.nix -iA build.x86_64-linux

there is a gui way also 5b. login into localhost - create project - and jobset with web gui - create input ()