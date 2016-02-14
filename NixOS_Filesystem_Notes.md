---
title: NixOS Filesystem Notes
permalink: /NixOS_Filesystem_Notes/
---

NixOS Filesystem
================

If you discover secrets about the NixOS Filesystem, such as how they are created or their purpose, add it here.

| Directory/File | Notes | Related Links |-------- | | `/run/current-system/sw` | | Modules add paths to packages into `config.environment.systemPackages` attribute. This is copied into the `config.system.path` attribute, which creates symlinks from the `/run/current-system/sw` directory into derivations in the Nix store. | | [system-path.nix](https://github.com/NixOS/nixpkgs/blob/master/nixos/modules/config/system-path.nix)
                                                                                                                                                                                                                                                                                                                                      [top-level.nix](https://github.com/NixOS/nixpkgs/blob/master/nixos/modules/system/activation/top-level.nix) &lt;!-- |--------  | | `(directory/file)` | | (notes) | | (related links) --&gt; |
|----------------|-------|-------------------------|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------|----------------------|-----------|--------------------------|

