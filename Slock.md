---
title: Slock
permalink: /Slock/
---

Setup
-----

### cannot disable the out-of-memory killer for this process

slock requires root for accessing passwords and protecting itself from OOM killing. To fix this error, add slock to your system packages and setuidPrograms by modifying your global configuration.nix like so:

`
  environment.systemPackages = with pkgs; [ slock ];
  security.setuidPrograms = [ "slock" ];
`