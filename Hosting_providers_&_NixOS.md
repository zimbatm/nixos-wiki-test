---
title: Hosting providers & NixOS
permalink: /Hosting_providers_&_NixOS/
---

Background
----------

NixOS is using very recent software & kernels by default. If you try to install NixOS on virtualizing systems be prepared that you have to patch some libraries to make them work with older kernels.

Hosting providers known to run NixOS
------------------------------------

www.linode.com:

-   Rob Vermaas and Marc Weber and Calvin Cheng are running NixOS on linode.com - see [Install NixOS on Linode](/Install_NixOS_on_Linode "wikilink").
-   Linode uses Xen virtualization and the approach to handling that is to use pv-grub-x86_64 option to boot up the appropriate NixOS-supplied kernel.
-   Newer instances (as of June 2015) on Linode uses KVM virtualization and for that, use the Direct Disk boot option as described here - <https://nixos.org/wiki/Install_NixOS_on_Linode#Using_the_upgraded_KVM_Linode_Direct_Disk_boot_and_grub_2_.28with_GPT.2FBIOS.29>

netcup.de:

-   Very easy to set-up (you can use any .iso file) and reliable. You get access to it via a flash based VNC client which even gives you access to the grub boot menu, so you can easily rollback to an earlier generation if you want.

supremebytes.com:

-   KVM hosting with custom ISO images on <http://www.supremebytes.com/business-virtual-servers.php>

ramnode.com:

-   KVM hosting where it is possible to install NixOS. They added an option to mount NixOS ISO, prior to that you could use SysRescCD with advices from here [Deploying NixOS on a 256RAM RamMode VPS](http://nixaid.com/deploying-nixos-256-ram-vps/)

NixOs experiences about other providers
---------------------------------------

-   vc-server.de:

` If you remove /bin/cp your data is lost. Older but probably very stable kernels.`
` I got a stystem running using some kind of bind mound magic keeping the original debian in place.`
` However I gave up updating it. It's still running my irssi client and hosting my .git repositories.`
` Eg I had to patch coreutils because touch failed. This was fixed in a newer corutils release.`
` This actually means you can't use binaries at all.`

-   OVH:

` Nice: You can use an API to switch to the rescue system, You probably can scale easily by API.`
`       You get kind of root server. Thus running virtualized systems yourself should be possible.`
` NixOs booted in kvm mode (Qemu/ vnc like interface). However when upstart was started it restarted.`
` When booting from hd the normal way nothing happened and I didn't know how to debug it.`