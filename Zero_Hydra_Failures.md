---
title: Zero Hydra Failures
permalink: /Zero_Hydra_Failures/
---

Currently hydra nixpkgs fails to build about 10200 jobs out of about 36200 total jobs. The purpose of this project is to drop the number of failing jobs to zero. Reasons:

-   That number is so high that it's not very informative anymore. Reading that an evaluation stepped from 0 to 100 failing packages is much more informative than from 10000 to 10200. Even if hydra has the lovely "new failing jobs" feature.
-   Dropping to 0 means less builds. If a derivation is broken since a month, every time a dependency changes hydra has to rebuild it, and we know beforehand it will be 100% a failure, leading to a huge waste of processing power and disk space. Either fix it, or mark it as broken.
-   If it's not built by hydra, there's no binary cache. The user thinks it's available, will download the sources, compile it (and that's already a WTF) and will fail. Very bad user experience.

The ZHF project aims to: either fix the build failures or mark packages as broken, in order to deliver a better user experience and lighten the hydra build machines.

Where are broken packages
-------------------------

This is the list of build failures of the latest nixpkgs tip: <http://hydra.nixos.org/jobset/nixpkgs/trunk/latest-eval#tabs-still-fail> .

Ignore the yellow X, which are failures due to time outs or other problems related to hydra building machines. Only consider packages with the red X that are failing since at least one month. Also make sure it's failing for your system (some may be failing on darwin but not on linux).

Follows data from Dolstra updated at 8 Aug.

For reference, here is a list jobs that have been broken for at least 6 months: <http://nixos.org/~eelco/.bla/broken-6-months.txt>

Jobs that have never been built successfully (which may include new jobs) <http://nixos.org/~eelco/.bla/broken-forever.txt>

Be fixed or be broken
---------------------

Fixing the build might not be always a pleasure. However sometimes you may have some spare time, and you are willing to fix some build failures of our lovely nixpkgs repository. My suggestion is to:

1.  Set up a nix-shell for the project
2.  Build it with "genericBuild"
3.  Try to find the culprit, if necessary write some patches
4.  If it builds, commit and push or create a pull request. Don't bother much on whether it runs correctly or not.
5.  If it's too hard, write down here what's the problem (it's not necessary to be very detailed, but try to write a few words about what you found out)
6.  After it's been written to this page and nobody ever cared about the build failure, mark it as broken.

In other words, don't let this project be a stressing task for you. Just try getting a nix-shell for it and build it. It only takes a couple of commands in your shell to do so. Fix it, or mark as broken. Don't spend too much time on it.

If the build is broken since months, chances are nobody (including the maintainers) is interested in it anymore, and you are not in charge of fixing it at all costs. Do it for fun, for the community and for hydra/nixpkgs health.

I prefer writing issues down here to avoid cluttering the github bug tracker, where instead we track problems of packages whose people are interested in.

Hard to fix
-----------

List of packages that you've tried fixing and that are hard to fix but still worth fixing. Try to list the culprits, cite your nick and the date of your trial. If nobody cares, these packages will be marked as broken.