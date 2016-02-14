---
title: Wireshark
permalink: /Wireshark/
---

From [<https://www.mail-archive.com/nix-commits@lists.science.uu.nl/msg09639.html> this commit on wireshark](/https://www.mail-archive.com/nix-commits@lists.science.uu.nl/msg09639.html_this_commit_on_wireshark "wikilink")

<code> environment.systemPackages = \[ pkgs.wireshark \];

` security.setuidOwners = [`
`   { program = "dumpcap";`
`     owner = "root";`
`     group = "wireshark";`
`     setuid = true;`
`     setgid = false;`
`     permissions = "u+rx,g+x";`
`   }`
` ];`
` users.extraGroups.wireshark.gid = 500;`

<code>