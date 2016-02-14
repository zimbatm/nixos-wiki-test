---
title: Talk:Fonts
permalink: /Talk:Fonts/
---

Some fonts (like Calibri) come with embedded bitmaps, which can result in ugly rendering. To turn this off the normal way is to symlink a piece of configuration into `/etc/fonts/conf.d` but that is not the Nix Way.

The Nix Way would e.g. be to make a configuration option per file under `${fontconfig}/share/fontconfig/conf.avail/` but currently there is nothing provided in the font configuration, so the only option left is to create `config.environment.etc."fonts/conf.d/foo.conf"` manually.