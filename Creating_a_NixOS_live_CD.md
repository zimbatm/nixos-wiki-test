---
title: Creating a NixOS live CD
permalink: /Creating_a_NixOS_live_CD/
---

motivation
----------

creating a modified NIXOS live CD out of a working nixos installation gives you the benefits:

-   verify what is included
-   no need to download it
-   it is easy to add your own programs to the image

how to build a live image
-------------------------

we will use the NIXPKGS variable, which points to the nixpkgs/nixos checkouts, see [Create and debug nix packages](/Create_and_debug_nix_packages "wikilink")

run command as follow (for 64 bit):

`export NIX_PATH=$NIXPKGS`
`nix-build -A iso_graphical.x86_64-linux $NIXPKGS/nixos/release.nix`

for 32 bit:

`export NIX_PATH=$NIXPKGS`
`nix-build -A iso_graphical.i686-linux $NIXPKGS/nixos/release.nix`

once the building process is over, you should see multiple nix distributions built (for multiple archs). See contents to find iso image path:

`$ cat /nix/store/2y4bivmrzg4d2dgby2b7prvfj7445088-nixos-iso-0.1pre1234/nix-support/hydra-build-products `
`file iso /nix/store/94rrfklbk2hcqhkr4627vsrlprlyva50-iso9660-image/iso/nixos-minimal-0.1pre1234-i686-linux-test.iso`

and copy the image to your USB drive (or use unetbootin).

adding additional software to the image
---------------------------------------

the basic script **/etc/nixos/nixos/release.nix** contains several entry points for custom images. as we are using **-A iso_minimal** have a look at: iso_minimal which will redirect us to:

-   ./modules/installer/cd-dvd/installation-cd-minimal.nix -&gt; /etc/nixos/nixos/modules/installer/cd-dvd/installation-cd-minimal.nix

`# This module defines a small NixOS installation CD.  It does not`
`# contain any graphical stuff.`
`{config, pkgs, ...}:`
`{`
`  require = [`
`    ./installation-cd-base.nix`
`    ../../profiles/minimal.nix`
`  ];`
`}`

### declarativly adding password/pubkey(s)

setting a default password on the boot cdrom (so that nobody can login using the default 'unset' password:

`   users.extraUsers.root.password="bar";`

or use this:

`  users.extraUsers.root.openssh.authorizedKeys.keys = [ "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDkWOKwPJqTu//XC8TsS8sDFVvrhluf5vyKaANxe9A5MJNAoSffOLogYA+igqvaWDZiHLZ7zaSL4IR30zw2Nr9UvMsHePBQLdUTsJxCxFI4ClidxiFN5bLhxhjz2Dyw0GaePLLN38TSSkI2Web2U8j6iJhx+u3RwxjnuztTXrgZsWvBh/1Z++XeGfCi4zCzGHtBIoY3bGtO9YqFSZVq+uePLQFdnNCeIFOgKu70yfEV+WpUAJDMF51MTCc574sDPLZB3lpE2YVggpVXXrJ0lm/ANtZ0UPeBPaZf7sW5vNrMAuW7btbywIzIj0tWmJw1kSZliyqS+E0XYICUvENrZpjt joachim@lenovo-t530" ];`

software installation inside the 'once' deployed and booted image
-----------------------------------------------------------------

in case you have booted from your image you can add software as described here:

-   [install/remove software](/install/remove_software "wikilink")

Without NixOS you need to supply the [source](https://svn.nixos.org/repos/nix/nixos/trunk/) instead of /etc/nixos/nixos.

references
----------

-   See also section "Building your own NixOS CD" of the [NixOS manual](http://nixos.org/nixos/manual/index.html#sec-building-cd).

[Category:NixOS](/Category:NixOS "wikilink") [Category:Installation](/Category:Installation "wikilink") [Category:Deployment](/Category:Deployment "wikilink")