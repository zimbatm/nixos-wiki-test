---
title: Binary Cache
permalink: /Binary_Cache/
---

A binary cache is a kind of [substitute](https://nixos.org/nix/manual/#gloss-substitute) that nix uses to download code-build store paths from.

Channels come with a binary cache, the NixOS channels use <https://cache.nixos.org>.

Using Binary Caches
-------------------

### In nix.conf

See the documentation of `binary-caches, trusted-binary-caches` and `extra-binary-caches` in <https://nixos.org/nix/manual/#sec-conf-file>.

In short, everyone may access all binary caches in binary-caches and trusted-binary-caches, but only privileged users may access any binary cache.

### On The Command Line

Use `--option binary-caches` to add(/replace?!) a cache. As unprivileged user it must be in `binary-caches` or `trusted-binary-caches`.

### In NixOS Config

Use the options `binaryCaches` and `trustedBinaryCaches` in the `nix` attribute set.