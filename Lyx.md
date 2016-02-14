---
title: Lyx
permalink: /Lyx/
---

To use lyx with texlive create a custom texlive “bundle”

      mytexlive = with pkgs; texLiveAggregationFun {
        paths = [ texLive texLiveBeamer ... ];
      };

and add `lyx` and `mytexlive` to your environment.

You then need to start lyx and do `Tools → Reconfigure` in order for Lyx to pick up the changes. Restart and it should work for your user.

See also [TexLive_HOWTO](/TexLive_HOWTO "wikilink").