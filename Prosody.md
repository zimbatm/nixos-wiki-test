---
title: Prosody
permalink: /Prosody/
---

    services.prosody.enable = true;

And of course you can configure it, e.g. like this:

    services.prosody = {

      enable = true;

      allowRegistration = false;

      modules = {
        roster = false;
        websocket = true;
      };

      virtualHosts.foo = {
        enabled = true;
        domain = "example.org";
        ssl = {
          key = "certs/example.org.key";
          cert = "certs/example.org.crt";
        };
      };

      admins = [ "root@example.org" "admin@mycomp.example.org"];

      extraConfig = ''
        http_default_host ="example.org";
        cross_domain_websocket = true;

        Component "mycomp.example.org"
          component_secret = "secret"
        '';
    };