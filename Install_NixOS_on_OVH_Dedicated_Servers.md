---
title: Install NixOS on OVH Dedicated Servers
permalink: /Install_NixOS_on_OVH_Dedicated_Servers/
---

This is the settings I used to install NixOS on a pair of SoYouStart (OVH/KimSufi) servers: You need to replace the CAPS by the info given by OVH. For more detailed information you can check out this post [<http://aborsu.github.io/2015/09/26/Install%20NixOS%20on%20So%20You%20Start%20dedicated%20server/> Install NixOS on a SoYouStart dedicated server.](/http://aborsu.github.io/2015/09/26/Install%20NixOS%20on%20So%20You%20Start%20dedicated%20server/_Install_NixOS_on_a_SoYouStart_dedicated_server. "wikilink")

      # Define your hostname.
      networking.hostName = "HOSTNAME";

      # IPv4 settings
      networking.interfaces.eth0.ip4 = [ { address = "IPV4_GATEWAY"; prefixLength = 24; } ];
      networking.defaultGateway = "IPV4_GATEWAY";
      networking.nameservers = [ "IPV4_NAMESERVER" ];

      # IPv6 settings
      networking.localCommands =
        ''
          ip -6 addr add IPV6_ADDRESS/64 dev eth0
          ip -6 route add IPV6_GATEWAY dev eth0
          ip -6 route add default via IPV6_GATEWAY
        '';

**Note:** Remember to enable ssh and to create a user or you will be locked out of your server.