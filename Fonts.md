---
title: Fonts
permalink: /Fonts/
---

### Installation

1.  Pick a few fonts (`nix-env -qaP|grep -i font` or look at <https://github.com/NixOS/nixpkgs/tree/master/pkgs/data/fonts> )
2.  Put them in `configuration.nix`:

` {`
`   fonts = {`
`     enableFontDir = true;`
`     enableGhostscriptFonts = true;`
`     fonts = with pkgs; [`
`       corefonts  # Micrsoft free fonts`
`       inconsolata  # monospaced`
`       ubuntu_font_family  # Ubuntu fonts`
`     ];`
`   };`
` }`

### Caveats

Though we can create local.conf with environment.etc."fonts/local.conf" or even create /etc/fonts/conf.d/99-overrideall to tweak fontconfig, those file are evaluated after the files in ${fontconfig}/etc/fonts/conf.d regardless of the prefix number.

Please join the discussion at <https://github.com/NixOS/nixpkgs/issues/1827> if interested.

### Debugging

The global Fontconfig configuration is kept in `/etc/fonts/fonts.conf`. This file is generated automatically, and should not be edited directly, but it can be useful to see what is actually written there. If you enable `enableFontDir`, all fonts are available under `/run/current-system/sw/share/X11-fonts` as well

You can also use `xlsfonts` to see which fonts are available to X.

Rebuild Font Cache
------------------

If some fonts appear distorted, e.g. characters are invisible, or not anti-aliased you may need to rebuild the font cache:

    $ fc-cache --really-force --verbose