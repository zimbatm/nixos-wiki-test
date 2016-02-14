---
title: How to add files to the nix-store
permalink: /How_to_add_files_to_the_nix-store/
---

Local files
-----------

To introduce a file into the nix store, you normally either use

`nix-store --add-fixed sha256 largefile`

or

`nix-prefetch-url `[`file://$(pwd)/largefile`](file://$(pwd)/largefile)

Unfortunately, this tries to load the whole file into memory and thus fails if it doesn't fit there.

Properly adding a large file on your own is a three step procedure:

     # find the right store path
     nix-store --print-fixed-path sha256 $(nix-hash --type sha256 --flat --base32 largefile) largefile
     # copy/move the file there (as root)
     mount -o remount,rw /nix/store
     mv largefile /nix/store <store-path>
     mount -o remount,ro /nix/store
     # register the file
     nix-store --register-validity --reregister RET
     <store-path> RET
     RET
     0 RET
     C-d

Remote files
------------

`nix-prefetch-url` uses curl and will accept most common URL's. It also supports a mirror:// scheme, see [the source](https://github.com/NixOS/nixpkgs/blob/master/pkgs/build-support/fetchurl/mirrors.nix) for the list of supported mirrors.

Specialized retrieval
---------------------

[pkgs/build-support](https://github.com/NixOS/nixpkgs/tree/master/pkgs/build-support) has a number of other fetching scripts; you can install a subset of them for local use using the [nix-prefetch-scripts](https://github.com/NixOS/nixpkgs/blob/master/pkgs/tools/package-management/nix-prefetch-scripts/default.nix) package.

nix-prefetch-git
----------------

This fetches a specified revision or tag from the specified URL. You can make it much faster by pointing to a local clone of the relevant repository:

` GIT_ALTERNATE_OBJECT_DIRECTORIES=$PWD/nixpkgs/.git/objects/ nix-prefetch-git `[`https://github.com/NixOS/nixpkgs.git`](https://github.com/NixOS/nixpkgs.git)` refs/heads/release-14.04`