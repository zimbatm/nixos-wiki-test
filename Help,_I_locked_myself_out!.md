---
title: Help, I locked myself out!
permalink: /Help,_I_locked_myself_out!/
---

For [various reasons](https://github.com/NixOS/nixpkgs/issues/7308) you can lock yourself out of NixOS.

There are two ways back in:

A live CD/USB
-------------

Boot from it, and set a new passwd. Then open /etc/shadow in your favourite editor, look up the line of the user that did passwd, and copy whatever is between the first two colons ( : ) to the equivalent line in /mnt/etc/passwd/.

Now you can boot NixOS again and log in with your new password.

edit grub
---------

The second way to get in, is to edit a grub command to say init=/bin/sh and boot it. Then do

`# export PATH=/nix/var/nix/profiles/system/sw/bin:/nix/var/nix/profiles/system/sw/sbin`

From there you can edit /etc/shadow. If you remove the string between the first two colons, you get an empty password.

Remember to set users.mutableUsers to true!