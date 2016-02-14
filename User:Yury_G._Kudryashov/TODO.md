---
title: User:Yury G. Kudryashov TODO
permalink: /User:Yury_G._Kudryashov/TODO/
---

Prepare/push patches to upstream
================================

-   gvfs: respect XDG_DATA_DIRS, split into gvfs-core (library+daemon) and gvfs-backends.

Common interface to external software archives
==============================================

Requirements
------------

-   Automatically generate nix expressions for the whole archive
-   Separate auto-generated data from Nixpkgs overrides
-   Auto-generated data outside of the main nixpkgs tree (not a hard requirement)
-   Nixpkgs 'global' overrides and per-user overrides

List of archives
----------------

-   CPAN
-   CTAN (TexLive?)
-   Ruby gems
-   Hackage

Remove packages with dead upstream
==================================