---
title: Fix gawk segfault on OSX 10.10
permalink: /Fix_gawk_segfault_on_OSX_10.10/
---

[This commit](https://github.com/NixOS/nixpkgs/commit/dad86b4f544db4d497e4bbe769dec0bb3e9cfbd7) causes all OSX machines to use the 10.9 SDK, which is the wrong thing to do on 10.10. To fix:

    sudo xcode-select --switch /