---
title: Gitlab
permalink: /Gitlab/
---

gitlab
------

### configuration.nix extensions

the gitlab service in nixos seems to be self-contained and thus only needs this configuration:

`     services.gitlab = {`
`       enable = true;`
`       databasePassword = "gitlab";`
`     };`

### initial setup

after you visit:

`   $ netstat -tulpen | grep 8080`
`   tcp        0      0 127.0.0.1:8080          0.0.0.0:*               LISTEN      165        14082      1499/unicorn master `

just type this: 127.0.0.1:8080 into your browser:

`   username: root`
`   password: 5iveL!fe`

### reverse proxy setup

      services.httpd = {
        enable = true;
        enableSSL = false;
        adminAddr = "web0@example.org";
        virtualHosts =
        [

          {
            hostName = "webservice1";
            serverAliases = ["webservice1"];
            extraConfig = ''
              # prevent a forward proxy!
              ProxyRequests off

              # User-Agent / browser identification is used from the original client
              ProxyVia Off
              ProxyPreserveHost On

              <Proxy *>
              Order deny,allow
              Allow from all
              </Proxy>

              ProxyPass / http://127.0.0.1:8080/
              ProxyPassReverse / http://127.0.0.1:8080/
            '';
          }
        ];
      };

links
-----

-   \[1\] <https://wiki.archlinux.org/index.php/Gitlab#PostgreSQL> might give you a good idea

[Category:Services](/Category:Services "wikilink")