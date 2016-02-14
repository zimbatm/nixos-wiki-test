---
title: Zabbix
permalink: /Zabbix/
---


      networking = {
        firewall = {
          enable = true;
          allowedTCPPorts = [ 80 443 ];
        };
      };

      services.httpd = {
        enable = true;
        hostName = "localhost";
        adminAddr = "example@example.org";

        virtualHosts = [
          { hostName = "zabbix";
            extraSubservices = pkgs.lib.singleton {
              serviceType = "zabbix";
              hostName = "webhost1";
              urlPrefix = "";
              configFile = pkgs.writeText "zabbix.conf.php" ''
                <?php
                global $DB;

                $DB['TYPE']     = 'POSTGRESQL';
                $DB['SERVER']   = ''';
                $DB['PORT']     = '0';
                $DB['DATABASE'] = 'zabbix';
                $DB['USER']     = 'zabbix';
                $DB['PASSWORD'] = ''';
                $DB['SCHEMA']   = ''';

                $ZBX_SERVER      = 'localhost';
                $ZBX_SERVER_PORT = '10051';
                $ZBX_SERVER_NAME = ''';

                $IMAGE_FORMAT_DEFAULT = IMAGE_FORMAT_PNG;
                ?>
              '';
            };
          }
        ];
      };


      services.zabbixServer.enable = true;
      services.zabbixServer.dbServer = "";
      services.zabbixAgent.enable = true;

      services.postgresql = {
        enable = true;
        package = pkgs.postgresql92;
        authentication = pkgs.lib.mkOverride 10 ''
          local zabbix all ident map=zabbix
          local all all ident
        '';
        identMap = ''
          zabbix zabbix                        zabbix
          zabbix ${config.services.httpd.user} zabbix
        '';
        extraConfig = ''
          listen_addresses = '''
        '';
      };

default user
------------

your first login on <http://localhost/zabbix> (or <https://localhost/zabbix>)

    user: Admin
    pass: zabbix