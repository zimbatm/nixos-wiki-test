---
title: Talk:Bootstrapping NixOS on ARM
permalink: /Talk:Bootstrapping_NixOS_on_ARM/
---

Work needed to make this possible
---------------------------------

I wonder what work had to be done to make this possible. I mean the work adding "sheevaplug" platform to [nixpkgs](https://github.com/NixOS/nixpkgs).

(My interest comes from some thoughts/intentions about adding another platform. With a very different architecture. Of course, the situation with bootstrapping NoxOs described in this page looks very attractive, when compared to some other distributions, which would be difficult to automatically bootstrap from sources only, without any use of the binary packages.)--[imz](/User:Imz "wikilink") ([talk](/User_talk:Imz "wikilink")) 14:56, 3 February 2016 (UTC)

Searching for the answer, I first have looked for the source for `pkgs.platforms.sheevaplug`.

### the source for `pkgs.platforms.sheevaplug`

I have found it in <https://github.com/NixOS/nixpkgs/blob/2a7b5e9f3053d845daed84b14eb9e2adc73f53b0/pkgs/top-level/platforms.nix#L24> .

### other mentions of "sheevaplug"

[There](https://github.com/NixOS/nixpkgs/search?utf8=%E2%9C%93&q=sheevaplug) seem to be not so many mentions of "sheevaplug" in the sources (around 10).

Are these all the "entry points" to adding a new platform?--[imz](/User:Imz "wikilink") ([talk](/User_talk:Imz "wikilink")) 14:56, 3 February 2016 (UTC)

Is nix relying on the host Debian system for compilation here?
--------------------------------------------------------------

Another interesting thing about this bootstrap-from-sources procedure is:

Is it done without relying on any binary Nix packages or binary blobs included/referenced in Nix packages?

This is important if we try to imagine a bootstrap on an architecture previously unknown in nixpkgs. This would mean that there are no binary packages and no blobs available for this architecture.

How would this be possible then?

The first obvious answer I could come up with (possibly, not correct) is that nix knows how to use some tools from the host Debian system. Is this true? Where are these rules for relying on the host system?

### example: GCC

As I understand, normally, nix would use GCC from nixpkgs to compile other packages.

What if there is no pre-compiled GCC for this architecture?

It could either use the host GCC to compile at least the nix GCC package, or use some imaginary C compiler written in shell to compile its own GCC (then it would need to rely only on the shell interpreter).

(Of course, this procedure may not lead to success even then, i.e., using the host GCC to compile nix's own GCC, because there might be no support for the new architecture in upstream GCC. ARM is not such a case, of course. And this is a different question.)--[imz](/User:Imz "wikilink") ([talk](/User_talk:Imz "wikilink")) 20:30, 3 February 2016 (UTC)