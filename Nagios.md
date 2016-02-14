---
title: Nagios
permalink: /Nagios/
---

this article helps you getting nagios monitoring 'up and running' on nixos.

1. you /etc/nixos/configuration.nix file must be extended like shown in section *nagios configuration* below

2. you have to create these files

`     ./nagios/conf.d/contacts.cfg`
`     ./nagios/conf.d/generic-host.cfg`
`     ./nagios/conf.d/generic-service.cfg`
`     ./nagios/conf.d/hostgroups.cfg`
`     ./nagios/conf.d/localhost.cfg`
`     ./nagios/conf.d/services.cfg`
`     ./nagios/conf.d/timeperiods.cfg`
`     ./nagios/conf.d/commands.cfg`

in '/etc/nixos/nagios/conf.d'. in this step the actual nagios configuration takes place. i've attached an example configuration below.

nixos configuration
-------------------

### nagios service

      system.activationScripts.nagios-sudo = ''
        ln -sf "${pkgs.nagiosPluginsOfficial}/libexec/check_disk" /opt/bin/check_disk
        ln -sf "${pkgs.nagiosPluginsOfficial}/libexec/check_http" /opt/bin/check_http
        ln -sf "${pkgs.nagiosPluginsOfficial}/libexec/check_ssh"  /opt/bin/check_ssh
        ln -sf "${pkgs.nagiosPluginsOfficial}/libexec/check_ping"  /opt/bin/check_ping
        ln -sf "${pkgs.nagiosPluginsOfficial}/libexec/check_smtp"  /opt/bin/check_smtp
      '';

      services.nagios = {
        enable = true;
        objectDefs = [
          ./nagios/conf.d/contacts.cfg
          ./nagios/conf.d/generic-host.cfg
          ./nagios/conf.d/generic-service.cfg
          ./nagios/conf.d/hostgroups.cfg
          ./nagios/conf.d/localhost.cfg
          ./nagios/conf.d/services.cfg
          ./nagios/conf.d/timeperiods.cfg
          ./nagios/conf.d/commands.cfg
        ];
        plugins = with pkgs;
          [ nagiosPluginsOfficial opensmtpd procps gawk lm_sensors /var/lib/nagios/custom-plugins ];

        cgiConfigFile = pkgs.writeText "nagios.cgi.conf" ''
          main_config_file=${config.services.nagios.mainConfigFile}
          use_authentication=1let
          url_html_path=${config.services.nagios.urlPath}
          authorized_for_system_information=myuser
          authorized_for_system_commands=myuser
          authorized_for_configuration_information=myuser
          authorized_for_all_hosts=myuser
          authorized_for_all_host_commands=myuser
          authorized_for_all_services=myuser
          authorized_for_all_service_commands=myuser
          default_statusmap_layout=5
        '';
      };

### webserver

      services.nginx = {
        enable = true;
        httpConfig = ''
          error_log /var/log/nginx/error.log;
          server {
            access_log /var/log/nginx/access.log;
            listen 80;

            auth_basic "Restricted";
            auth_basic_user_file /etc/nixos/nagios/htpasswd.users;

            # php related sripts go to php-fpm
            location ~ ^${config.services.nagios.urlPath}/(.+\.php)(.*)$ {
              include        ${pkgs.nginx}/conf/fastcgi_params;
              fastcgi_split_path_info       ^${config.services.nagios.urlPath}/(.+\.php)(.*)$;
              fastcgi_pass   127.0.0.1:9000;
              fastcgi_param  SCRIPT_FILENAME  ${pkgs.nagios}/share/$fastcgi_script_name;
              fastcgi_param  REMOTE_USER        $remote_user;
            }

            location ${config.services.nagios.urlPath} {
              alias          ${pkgs.nagios}/share;
              index          index.php;
              #autoindex on;
            }

            # cgi scripts, basically c-programs, go to the fcgi
            location ~ ^${config.services.nagios.urlPath}/cgi-bin/.*\.cgi$ {
              fastcgi_pass   127.0.0.1:9001;
              fastcgi_split_path_info       ^${config.services.nagios.urlPath}/cgi-bin/(.+\.cgi)(.*)$;
              fastcgi_param  SCRIPT_FILENAME  ${pkgs.nagios}/sbin/$fastcgi_script_name;
              fastcgi_param  NAGIOS_CGI_CONFIG ${config.services.nagios.cgiConfigFile};
              fastcgi_param  REMOTE_USER        $remote_user;
              include        ${pkgs.nginx}/conf/fastcgi_params;
            }
          }
        '';
      };

### phpfpm

      services.phpfpm.poolConfigs = {
        nagios = ''
          listen = 127.0.0.1:9000
          user = nagios
          pm = dynamic
          pm.max_children = 4
          pm.start_servers = 4
          pm.min_spare_servers = 2
          pm.max_spare_servers = 4
          pm.max_requests = 500

          php_flag[display_errors] = on
          php_value[date.timezone] = "Europe/Berlin"
          php_admin_value[error_log] = /var/log/nagios/phpfpm.log
          php_admin_flag[log_errors] = on
          php_admin_value[open_basedir] = ${pkgs.nagios}/share
        '';
      };

### fcgiwrap

      services.fcgiwrap = {
        enable = true;
        preforkProcesses = 4;
        bindSocket = "tcp:127.0.0.1:9001";
     };

nagios config files
-------------------

here is an examples list of .cfg files i've been using. it is advisable to copy these over and start from there instead of creating them from scratch:

`      ./conf.d/timeperiods.cfg`
`      ./conf.d/nixos.org.cfg`
`      ./conf.d/generic-host.cfg`
`      ./conf.d/localhost.cfg`
`      ./conf.d/contacts.cfg`
`      ./conf.d/router.cfg`
`      ./conf.d/commands.cfg`
`      ./conf.d/nixcloud.io.cfg`
`      ./conf.d/hostgroups.cfg`
`      ./conf.d/nixos.png`
`      ./conf.d/generic-service.cfg`
`      ./conf.d`
`      ./htpasswd.users`

please create the **htpasswd.users** file manually using:

`   htpasswd -c /etc/nixos/nagios/htpasswd.users`

add a user called: myuser

`   cat htpasswd.users `
`   myuser:$apr1$iW2GfUNk$h.gAfIqjHZv.mntStcCng1`

in this example the **myuser** password is **myuser**

the htpasswd tool can be installed like this:

`   environment.systemPackages = with pkgs; [`
`     apacheHttpd`
`   ];`

### ./timeperiods.cfg

`   ###############################################################################`
`   # timeperiods.cfg`
`   ###############################################################################`
`   `
`   # This defines a timeperiod where all times are valid for checks, `
`   # notifications, etc.  The classic "24x7" support nightmare. :-)`
`   `
`   define timeperiod{`
`           timeperiod_name 24x7`
`           alias           24 Hours A Day, 7 Days A Week`
`           sunday          00:00-24:00`
`           monday          00:00-24:00`
`           tuesday         00:00-24:00`
`           wednesday       00:00-24:00`
`           thursday        00:00-24:00`
`           friday          00:00-24:00`
`           saturday        00:00-24:00`
`           }`
`   `
`   # Here is a slightly friendlier period during work hours`
`   define timeperiod{`
`           timeperiod_name workhours`
`           alias           Standard Work Hours`
`           monday          09:00-17:00`
`           tuesday         09:00-17:00`
`           wednesday       09:00-17:00`
`           thursday        09:00-17:00`
`           friday          09:00-17:00`
`           }`
`   `
`   # The complement of workhours`
`   define timeperiod{`
`           timeperiod_name nonworkhours`
`           alias           Non-Work Hours`
`           sunday          00:00-24:00`
`           monday          00:00-09:00,17:00-24:00`
`           tuesday         00:00-09:00,17:00-24:00`
`           wednesday       00:00-09:00,17:00-24:00`
`           thursday        00:00-09:00,17:00-24:00`
`           friday          00:00-09:00,17:00-24:00`
`           saturday        00:00-24:00`
`           }`
`   `
`   # This one is a favorite: never :)`
`   define timeperiod{`
`           timeperiod_name never`
`           alias           Never`
`           }`
`   `
`   # end of file`

### ./nixos.org.cfg

`   ######################### nixos.org ##############################`
`   define host{`
`           use                     generic-host            ; Name of host template to use`
`           host_name               nixos.org`
`           alias                   nixos.org `
`           address                 nixos.org`
`           parents                 router`
`   }`
`   `
`   define service{`
`           use                             generic-service         ; Name of service template to use`
`           host_name                       nixos.org`
`           service_description             HTTP`
`           check_command                   check_http`
`   }`
`   `
`   define service{`
`           use                             generic-service         ; Name of service template to use`
`           host_name                       nixos.org`
`           service_description             SSH`
`           check_command                   check_ssh!22`
`   }`
`   `

### ./generic-host.cfg

`   # Generic host definition template - This is NOT a real host, just a template!`
`   `
`   define host{`
`           name                            generic-host    ; The name of this host template`
`           notifications_enabled           1       ; Host notifications are enabled`
`           event_handler_enabled           1       ; Host event handler is enabled`
`           flap_detection_enabled          1       ; Flap detection is enabled`
`           process_perf_data               1       ; Process performance data`
`           retain_status_information       1       ; Retain status information across program restarts`
`           retain_nonstatus_information    1       ; Retain non-status information across program restarts`
`              check_command                   check_ping`
`              max_check_attempts              10`
`              notification_interval           0`
`              notification_period             24x7`
`              notification_options            d,u,r`
`              contact_groups                  admins`
`           register                        0       ; DONT REGISTER THIS DEFINITION - ITS NOT A REAL HOST, JUST A TEMPLATE!`
`           }`

### ./localhost.cfg

`   # A simple configuration file for monitoring the local host`
`   # This can serve as an example for configuring other servers;`
`   # Custom services specific to this host are added here, but services`
`   # defined in nagios2-common_services.cfg may also apply.`
`   # `
`   `
`   define host{`
`           use                     generic-host            ; Name of host template to use`
`           host_name               localhost`
`           alias                   localhost`
`           parents                 router`
`           address                 127.0.0.1`
`   }`
`   `
`   define host{`
`           use                     generic-host            ; Name of host template to use`
`           host_name               localhost1`
`           alias                   localhost1`
`           parents                 router`
`           address                 127.0.0.1`
`   }`
`   `
`   define host{`
`           use                     generic-host            ; Name of host template to use`
`           host_name               localhost2`
`           alias                   localhost2`
`           parents                 router`
`           address                 127.0.0.1`
`   }`
`   `
`   define host{`
`           use                     generic-host            ; Name of host template to use`
`           host_name               localhost3`
`           alias                   localhost3`
`           parents                 localhost1`
`           address                 127.0.0.1`
`   }`
`   define host{`
`           use                     generic-host            ; Name of host template to use`
`           host_name               localhost4`
`           alias                   localhost4`
`           parents                 localhost1`
`           address                 127.0.0.1`
`   }`
`   define host{`
`           use                     generic-host            ; Name of host template to use`
`           host_name               localhost5`
`           alias                   localhost5`
`           parents                 localhost1`
`           address                 127.0.0.1`
`   }`
`   define host{`
`           use                     generic-host            ; Name of host template to use`
`           host_name               localhost6`
`           alias                   localhost6`
`           parents                 localhost1`
`           address                 127.0.0.1`
`   }`
`   `

### ./contacts.cfg

`   ###############################################################################`
`   # contacts.cfg`
`   ###############################################################################`
`   `
`   `
`   `
`   ###############################################################################`
`   ###############################################################################`
`   #`
`   # CONTACTS`
`   #`
`   ###############################################################################`
`   ###############################################################################`
`   `
`   # In this simple config file, a single contact will receive all alerts.`
`   `
`   define contact{`
`           contact_name                    root`
`           alias                           Root`
`           service_notification_period     24x7`
`           host_notification_period        24x7`
`           service_notification_options    w,u,c,r`
`           host_notification_options       d,r`
`           service_notification_commands   notify-service-by-email`
`           host_notification_commands      notify-host-by-email`
`           email                           root@localhost`
`           }`
`   `
`   `
`   `
`   ###############################################################################`
`   ###############################################################################`
`   #`
`   # CONTACT GROUPS`
`   #`
`   ###############################################################################`
`   ###############################################################################`
`   `
`   # We only have one contact in this simple configuration file, so there is`
`   # no need to create more than one contact group.`
`   `
`   define contactgroup{`
`           contactgroup_name       admins`
`           alias                   Nagios Administrators`
`           members                 root`
`           }`

### ./router.cfg

`   define host{`
`           use                     generic-host            ; Name of host template to use`
`           host_name               router`
`           alias                   router`
`           address                 192.168.1.1`
`   }`
`   `

### ./commands.cfg

`   ###############################################################################`
`   # COMMANDS.CFG - SAMPLE COMMAND DEFINITIONS FOR NAGIOS `
`   ###############################################################################`
`   `
`   `
`   ################################################################################`
`   # NOTIFICATION COMMANDS`
`   ################################################################################`
`   `
`   `
`   # 'notify-host-by-email' command definition`
`   # obviously broken on nixos as /usr/bin/printf and /usr/bin/mail don't exist!`
`   define command{`
`       command_name    notify-host-by-email`
`       command_line    /usr/bin/printf "%b" "***** Nagios *****\n\nNotification Type: $NOTIFICATIONTYPE$\nHost: $HOSTNAME$\nState: $HOSTSTATE$\nAddress: $HOSTADDRESS$\nInfo: $HOSTOUTPUT$\n\nDate/Time: $LONGDATETIME$\n" | /usr/bin/mail -s "** $NOTIFICATIONTYPE$ Host Alert: $HOSTNAME$ is $HOSTSTATE$ **" $CONTACTEMAIL$`
`       }`
`   `
`   # 'notify-service-by-email' command definition`
`   define command{`
`       command_name    notify-service-by-email`
`       command_line    /usr/bin/printf "%b" "***** Nagios *****\n\nNotification Type: $NOTIFICATIONTYPE$\n\nService: $SERVICEDESC$\nHost: $HOSTALIAS$\nAddress: $HOSTADDRESS$\nState: $SERVICESTATE$\n\nDate/Time: $LONGDATETIME$\n\nAdditional `[`Info:\n\n$SERVICEOUTPUT$\n`](Info:\n\n$SERVICEOUTPUT$\n)`" | /usr/bin/mail -s "** $NOTIFICATIONTYPE$ Service Alert: $HOSTALIAS$/$SERVICEDESC$ is $SERVICESTATE$ **" $CONTACTEMAIL$`
`       }`
`   `
`   `
`   `
`   `
`   `
`   ################################################################################`
`   # HOST CHECK COMMANDS`
`   ################################################################################`
`   `
`   # On Debian, check-host-alive is being defined from within the`
`   # nagios-plugins-basic package`
`   `
`   ################################################################################`
`   # PERFORMANCE DATA COMMANDS`
`   ################################################################################`
`   `
`   `
`   # 'process-host-perfdata' command definition`
`   define command{`
`       command_name    process-host-perfdata`
`       command_line    /usr/bin/printf "%b" "$LASTHOSTCHECK$\t$HOSTNAME$\t$HOSTSTATE$\t$HOSTATTEMPT$\t$HOSTSTATETYPE$\t$HOSTEXECUTIONTIME$\t$HOSTOUTPUT$\t$HOSTPERFDATA$\n" >> /var/lib/nagios3/host-perfdata.out`
`       }`
`   `
`   `
`   # 'process-service-perfdata' command definition`
`   define command{`
`       command_name    process-service-perfdata`
`       command_line    /usr/bin/printf "%b" "$LASTSERVICECHECK$\t$HOSTNAME$\t$SERVICEDESC$\t$SERVICESTATE$\t$SERVICEATTEMPT$\t$SERVICESTATETYPE$\t$SERVICEEXECUTIONTIME$\t$SERVICELATENCY$\t$SERVICEOUTPUT$\t$SERVICEPERFDATA$\n" >> /var/lib/nagios3/service-perfdata.out`
`       }`
`   `
`   `
`   `
`   define command{`
`           command_name    check_ping`
`           command_line    /opt/bin/check_ping -H $HOSTADDRESS$ -w 100,20% -c 100,60%`
`   }`
`   `
`   define command{`
`           command_name    check_ssh`
`           command_line    /opt/bin/check_ssh -p $ARG1$ '$HOSTADDRESS$' `
`   }`
`   `
`   define command{`
`           command_name    check_http`
`           command_line    /opt/bin/check_http '$HOSTADDRESS$'`
`   }`
`   define command{`
`           command_name    check_smtp`
`           command_line    /opt/bin/check_smtp -H '$HOSTADDRESS$' -4 -S -A LOGIN -U $ARG1$ -P $ARG2$ `
`   }`

### ./nixcloud.io.cfg

`   define host{`
`           use                     generic-host            ; Name of host template to use`
`           host_name               nixcloud.io`
`           alias                   nixcloud.io`
`           address                 nixcloud.io`
`           parents                 router`
`   }`
`   `
`   #define service{`
`   #        use                             generic-service         ; Name of service template to use`
`   #        host_name                       nixcloud.io`
`   #        service_description             SMTP`
`   #        check_command                   check_smtp!foo@dune2.de!foomail1`
`   #}`
`   `
`   define service{`
`           use                             generic-service         ; Name of service template to use`
`           host_name                       nixcloud.io`
`           service_description             HTTP`
`           check_command                   check_http`
`   }`
`   `
`   define service{`
`           use                             generic-service         ; Name of service template to use`
`           host_name                       nixcloud.io`
`           service_description             SSH`
`           check_command                   check_ssh!20202`
`   }`
`   `
`   `
`   `

### ./hostgroups.cfg

`   # Some generic hostgroup definitions`
`   `
`   # A simple wildcard hostgroup`
`   #define hostgroup {`
`   #        hostgroup_name  all`
`   #          alias           All Servers`
`   #          members         *`
`   #        }`
`   `
`   # A list of your Debian GNU/Linux servers`
`   define hostgroup {`
`           hostgroup_name  debian-servers`
`              alias           GNU/Linux Servers`
`              members         localhost`
`           }`
`   `
`   # A list of your web servers`
`   #define hostgroup {`
`   #        hostgroup_name  http-servers`
`   #          alias           HTTP servers`
`   #          members         localhost`
`   #        }`
`   #`
`   ## A list of your ssh-accessible servers`
`   #define hostgroup {`
`   #        hostgroup_name  ssh-servers`
`   #          alias           SSH servers`
`   #          members         localhost`
`   #        }`
`   `
`   `

### ./generic-service.cfg

`   # generic service template definition`
`   define service{`
`           name                            generic-service ; The 'name' of this service template`
`           active_checks_enabled           1       ; Active service checks are enabled`
`           passive_checks_enabled          1       ; Passive service checks are enabled/accepted`
`           parallelize_check               1       ; Active service checks should be parallelized (disabling this can lead to major performance problems)`
`           obsess_over_service             1       ; We should obsess over this service (if necessary)`
`           check_freshness                 0       ; Default is to NOT check service 'freshness'`
`           notifications_enabled           1       ; Service notifications are enabled`
`           event_handler_enabled           1       ; Service event handler is enabled`
`           flap_detection_enabled          1       ; Flap detection is enabled`
`           process_perf_data               1       ; Process performance data`
`           retain_status_information       1       ; Retain status information across program restarts`
`           retain_nonstatus_information    1       ; Retain non-status information across program restarts`
`              notification_interval           0                ; Only send notifications on status change by default.`
`              is_volatile                     0`
`              check_period                    24x7`
`              normal_check_interval           5`
`              retry_check_interval            1`
`              max_check_attempts              4`
`              notification_period             24x7`
`              notification_options            w,u,c,r`
`              contact_groups                  admins`
`           register                        0       ; DONT REGISTER THIS DEFINITION - ITS NOT A REAL SERVICE, JUST A TEMPLATE!`
`           }`