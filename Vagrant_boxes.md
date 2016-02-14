---
title: Vagrant boxes
permalink: /Vagrant_boxes/
---

This page describes the options you have using NixOS for Vagrant-based projects.

Vagrant is currently not supported on a community-level. @theuni is currently trying to gather existing information and create some momentum to provide coherent and maintained support.

If you would like to help out (or if you are the author/maintainer of one of the mentioned pieces), please let @theuni know on the nix-dev mailinglist)

Plugins
-------

Plugins help to integrate a distribution nicely with the Vagrant mechanisms like setting the hostname in a multi-machine environment, configuring network, running provisioners, etc. We need a good plugin to make the support for the distribution a good experience on Vagrant. Without a plugin this will always be superficial.

-   <https://github.com/oxdi/vagrant-nixos> -- is missing a release and needs to include the name-setting fix pull request to support multi-machine setups. Otherwise this seems to be a good basis for native Vagrant support to integrate things like network management, hostname, custom provisioning, etc.

Packer
------

Packer helps creating Vagrant boxes "from scratch" by automating ISO downloads and typing into the virtual machine's console.

-   <https://github.com/zimbatm/nixbox> -- not integrated with the plugins, but have Atlas boxes available and I was able to create my own plugin-compatible boxes, also, currently only 14.04, but there's a branch that I'm trying to fix for 14.12 (and then update to 15.06)
-   <https://github.com/oxdi/nixos> -- theoretically those are integrated with the plugin but do not have Atlas boxes available
-   <https://github.com/cstrahan/nix-packer/pull/14> -- a pull request for cstrahans packer setup to support 14.12

Base boxes
----------

Generally you can discover Vagrant base boxes by \[<https://atlas.hashicorp.com/boxes/search?utf8=%E2%9C%93&sort>=&provider=&q=nixos searching atlas\].

The ones I worked with so far:

-   zimbatm/nixbox32|64 -- those were the ones I played with to build my own
-   flyingcircus/nixos-14.12-i686 -- the boxes that I created to update to 14.12 and integrate the oxdi plugin, as well as some additions to support my environment

Players
-------

People or organizations on the net I've seen working on NixOS/Vagrant-related things. I'd love to get in contact but haven't necessarily been able to.

-   <https://github.com/oxdi> -- are maintaining base images and the plugin above. Unfortunately haven't gotten a response via their corporate email.
-   <https://github.com/zimbatm> -- created packer setups that I used for building the Flying Circus vagrant images for 14.12 and is willing to help the community, been in touch via email
-   <https://atlas.hashicorp.com/larryweya/> -- has a 14.12 minimal box available, but I didn't test it, not in contact
-   <https://atlas.hashicorp.com/cstrahan/> -- has 14.04 box available with the most downloads on Atlas, not in contact

Todo
----

-   Find community interest in maintaining those
-   Get the plugin ready and released
-   Get updated base images and publish them under a community-approved organisation
-   Find a workflow and committment to keep the base images and plugin updated
