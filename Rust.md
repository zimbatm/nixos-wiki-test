---
title: Rust
permalink: /Rust/
---

This page is about the Rust programming language.

Stable
------

You can get the stable version of Rust on `nixpkgs` as `nixpkgs.rustc`.

Nixpkgs does not currently store older versions of the compiler.

Beta
----

Nixpkgs does not currently store beta versions of the compiler either.

Nightlies
---------

For getting nightly builds, use [Ericson2314's rustc-nightly package](https://github.com/Ericson2314/nixos-configuration/blob/dell/user/.nixpkgs/rust-nightly.nix). It grabs the binaries from the Mozilla servers.

It can be used like this:

` settings = fetchFromGitHub {`
`   owner = "Ericson2314";`
`   repo = "nixos-configuration";`
`   rev = "036ef0cfefacc2b292a29f3ef0a1fac9c339f6b0";`
`   sha256 = "05n26kq3595j2q2wga20w3zh9cy2dnwmrni3jzggzbxln53cmd8w";`
` };`

` funs = callPackage "${settings}/user/.nixpkgs/rust-nightly.nix" { };`

` rustcNightly = funs.rustc {`
`   date = "2015-08-25";`
`   hash = "15h84x58fzwx38iqv85g9zq1kzlx8xhrc79c14i27lgm61q7ywdf";`
` };`

` cargoNightly = funs.cargo {`
`   date = "2015-08-20";`
`   hash = "16lb1ximivzp0v1afmv2538w6wvkln0wg0429lpg97n0j5rapi1i";`
` };`

You will have to update the date and hash yourself.

Note: Does not work on `darwin` because it calls `patchelf` directly.