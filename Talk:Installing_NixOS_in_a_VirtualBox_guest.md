---
title: Talk:Installing NixOS in a VirtualBox guest
permalink: /Talk:Installing_NixOS_in_a_VirtualBox_guest/
---

This did not work for me.

Download a NixOS ISO from <http://nixos.org> Add a New Machine in VirtualBox with OS Type "Linux / Other Linux" Base Memory Size: 768 MB New Hard Disk of 8 GB Mount the CD-ROM with the NixOS ISO (by clicking on CD/DVD-ROM) Click on Settings / Advanced (or possibly System / Processor) and enable PAE/NX Networking: select "Attached to: NAT". Change host-interface to "en1: Airport". Now you can install NixOS:

Specifically the networking. With this configuration eth0 did not get an IP address from my DHCP server.

This worked for me:

Networking: select "Attached to: Bridged".

After this change my machine received an IP and I was able to install.