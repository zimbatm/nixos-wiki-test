---
title: Talk:Disabling IPv6
permalink: /Talk:Disabling_IPv6/
---

At least on the current default linux kernel (2.6.35.14) on my x86_64 architecture the kernel parameter method effectively prevents my NICs from acquiring any IPv6 addresses at boot time. I am not sure about possible side effects, but my nixos box is not apparently affected in any negative way. This is probably a workaround for some unforseen problem in the networking nix expression or in the ability of current GNU/Linux tools to avoid loading IPv6 support once the kernel loads the appropriate module, which seems to happen by default. [Modulistic](/User:Modulistic "wikilink") 10:31, 31 October 2011 (UTC)