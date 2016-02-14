---
title: Installing hydra as nixos module
permalink: /Installing_hydra_as_nixos_module/
---

[Category:Hydra](/Category:Hydra "wikilink")

This wiki-page documents how to setup Hydra, which is used for building packages & continuous integration. This guide focuses on Hydra on top of NixOS and there is another manual [Installing Hydra on Ubuntu](/Installing_Hydra_on_Ubuntu "wikilink").

Things you will probably need:

-   Get the **hydra sources** from github: '<https://github.com/NixOS/hydra>'. Put them in a suitable place, for example **/root/hydra**.
-   Hydra configured as a NixOS service
-   **PostgreSQL** as a backend for Hydra
-   **OpenSSH** - for remote access
-   A webserver like **apache** or **nginx** configured as 'reverse proxy'
-   A mail-system (Optional)

Downloading the hydra source
----------------------------

    cd /root
    git clone https://github.com/NixOS/hydra

/etc/nixos/configuration.nix
----------------------------

An **example configuration.nix**:

         1  # Edit this configuration file to define what should be installed on
         2  # your system.  Help is available in the configuration.nix(5) man page
         3  # and in the NixOS manual (accessible by running ‘nixos-help’).
         4
         5  { config, pkgs, ... }:
         6
         7  let
         8    hydra = /root/hydra;
         9    #hydra = (import <nixpkgs> {}).fetchgit {
        10    #  url = https://github.com/NixOS/hydra;
        11    #  rev = "refs/heads/master";
        12    #  sha256 = "0dy20iimgfqpknyzs8rv78sm5yqpsxav2w3cbxf3r219wcwg437g";
        13    #};
        14  in
        15  {
        16    imports =
        17      [ # Include the results of the hardware scan.
        18        ./hardware-configuration.nix
        19      ];
        20
        21    require = [ "${hydra}/hydra-module.nix" ];
        22
        23    services = {
        24      postfix = {
        25       enable = true;
        26       setSendmail = true;
        27      };
        28
        29      hydra = {
        30          enable = true;
        31          dbi = "dbi:Pg:dbname=hydra;host=localhost;user=hydra;";
        32          package = (import "${hydra}/release.nix" {}).build.x86_64-linux;
        33          hydraURL = "https://hydra.example.com/";
        34          listenHost = "localhost";
        35          port = 3000;
        36          minimumDiskFree = 5;  # in GB
        37          minimumDiskFreeEvaluator = 2;
        38          notificationSender = "hydra@example.com";
        39          logo = null;
        40          debugServer = false;
        41      };
        42      # Hydra requires postgresql to run
        43      postgresql.enable = true;
        44      postgresql.package = pkgs.postgresql;
        45
        46      # frontend http/https server
        47      nginx.enable = true;
        48      nginx.config = pkgs.lib.readFile /root/nginx.conf;
        49      authentication = pkgs.lib.mkOverride 10 ''
        50        host hydra all 127.0.0.1/8 trust
        51        local all all trust
        52      '';
        53    };

Using PostgreSQL on Separate Server
-----------------------------------

It is possible to PostgreSQL running on a separate server by adding the location of the database to the configuration and it is then not required to run PostgreSQL on localhost.

         1  # Hydra:
         2  services.hydra = {
         3    enable = true;
         4    package = (import "${hydra}/release.nix" {}).build.x86_64-linux; # or i686-linux if appropriate.
         5    dbi = "dbi:Pg:dbname=hydra;host=database.example.com;user=hydra;";
         6    hydraURL = "https://hydra.example.com";
         7    notificationSender = "hydra@example.com";
         8  };

Building the configuration
--------------------------

Then, running 'nixos-rebuild switch' and rebooting, hydra initialises itself through systemd scripts, enabled as part of the installation.

Now you should be ready to log in to hydra. The password for user 'admin' is generated at initialisation time, and found in '/var/lib/hydra/.pgpass', which incidentally is also the password used by hydra to connect to postgresql.

The contents are:

And set the permission properly:

    chown hydra:hydra /var/lib/hydra/.pgpass
    chmod 0600 /var/lib/hydra/.pgpass

Running Hydra behind Nginx
--------------------------

Enable **Nginx** by adding the following to /etc/nixos/configuration.nix

         1  # frontend http/https server
         2  nginx.enable = true;
         3  nginx.config = ''
         4    error_log /var/log/nginx-error.log;
         5    events {}
         6
         7    http {
         8      server {
         9        listen 443 ssl;
        10        access_log /var/log/nginx-access.log;
        11        server_name hydra.example.com;
        12        ssl_certificate /root/ssl/hydra.crt;
        13        ssl_certificate_key /root/ssl/hydra.key;
        14        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        15        ssl_ciphers HIGH:!aNULL:!MD5;
        16        location / {
        17          proxy_set_header Host $http_host;
        18          proxy_set_header X-Forwarded-Host $http_host;
        19          proxy_set_header X-Forwarded-Proto https;
        20          proxy_set_header X-Forwarded-Port 443;
        21          proxy_set_header X-Real-IP $remote_addr;
        22          proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        23          proxy_set_header X-Request-Base "https://hydra.example.com";
        24          proxy_pass http://localhost:3000/;
        25        }
        26      }
        27    }
        28  '';

If you didn't have your certificates in /root/ssl before evaluating this configuration your nginx exits as it can't work without these SSL-certs. Therefore restart it:

`   systemctl restart nginx`

And see the log:

`   journalctl -u nginx`

PostgreSQL user-management
--------------------------

Now run these commands as user (root):

    $ createuser hydra -P
    Enter password for new role:
    Enter it again:

    $ createdb -O hydra hydra
    $ systemctl restart hydra-init

    $ echo "GRANT ALL ON DATABASE hydra TO hydra;" | psql hydra
    GRANT

Hydra user-management
---------------------

    su hydra -c "hydra-create-user admin --email-address 'hydra\@hydra.example.org' --password admin --role admin"
    creating new user `admin'

Hydra's webinterface
--------------------

If your DNS and machine names are set, just open:

`   `[`https://hydra.example.com`](https://hydra.example.com)

in your browser.

References
----------

Some helpful references

-   [Setting up a Hydra build cluster for continuous integration and testing (part 1)](http://sandervanderburg.blogspot.com/2013/04/setting-up-hydra-build-cluster-for.html)
-   [Setting up a Hydra build cluster for continuous integration and testing (part 2)](http://sandervanderburg.blogspot.com/2013/04/setting-up-hydra-build-cluster-for_10.html)
-   [Jump into Hydra](http://blog.matejc.com/blogs/myblog/jump-into-hydra/)
-   [NixOS + Hydra + Nginx](http://blog.matejc.com/blogs/myblog/nixos-hydra-nginx/)

Now, refer to the Hydra [manual](http://hydra.nixos.org/job/hydra/trunk/tarball/latest/download-by-type/doc-pdf/manual) for more info.