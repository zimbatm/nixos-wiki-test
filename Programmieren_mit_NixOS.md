---
title: Programmieren mit NixOS
permalink: /Programmieren_mit_NixOS/
---

Rust
----

Zum Programmieren mit Rust unter NixOS verwendet man genau wie unter anderen Linux-Distributionen am besten den Rust-Package-Manager "Cargo". Um diesen global zu installieren fügt man folgende Zeilen in seine \`environment.systemPackages\` ein:

`   pkgs.rustStable.rustc`
`   pkgs.rustStable.cargo`

oder, falls man eine \`nix-shell\` verwenden will:

`   { pkgs ? (import `<nixpkgs>` {}) }:`
`   `
`   let env = with pkgs.rustStable; [ rustc cargo ];`
`   in pkgs.stdenv.mkDerivation rec {`
`       name = "meinRustEnv";`
`       src = ./.;`
`       version = "0.0.0";`
`   `
`       buildInputs = [ env ];`
`   }`

Rust-Packete welche man benötigt können nun wie gewohnt über Cargo installiert werden.