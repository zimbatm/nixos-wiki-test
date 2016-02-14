---
title: Hydra release management
permalink: /Hydra_release_management/
---

*This is work in progress. We're currently trying to establish release management for hydra and aren't finished yet.*

Before the release
------------------

-   check changelog is really up to date
-   mark any backwards incompatible changes
-   make sure all hydra tests pass
-   make sure the upgrade procedure works from previous release
-   test that setting up hydra from scratch works by following installation guide (this could be automated with NixOS test)

At release time
---------------

-   create a git (annotated) tag
-   mark the release date in changelog, etc
-   post a mail to nix-dev

After release
-------------

-   bump the package in nixpkgs
