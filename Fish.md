---
title: Fish
permalink: /Fish/
---

Fish is the [Friendly Interactive SHell](http://fishshell.com/).

At the moment it’s not possible to use fish as login shell. But you can always start it as default shell of your terminal emulator.

Useful scripts
--------------

### Show that you are in a nix-shell

Add this to `fish/fish_prompt.fish`:

      set -l nix_shell_info (
        if test "$IN_NIX_SHELL" = "1"
          echo -n "<nix-shell> "
        end
      )

and `$nix_shell_info` to the echo in that function, e.g.:

    echo -n -s "$nix_shell_info ~>"

Now your prompt looks like this

-   outside: `~>`
-   inside: `<nix-shell> ~>`

btw. you can directly start nix-shell in fish with `nix-shell --run fish`, but (FIXME) the normal build functions are not available there.