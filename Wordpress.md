---
title: Wordpress
permalink: /Wordpress/
---

this guide shows how to manage a **single wordpress instance**. if you need more than one instance, it needs to be extended using a unique id system like in *mediawiki.nix*.

httpd extraSubservices example
------------------------------

add this to your *configuration.nix* file:

    let

    # For shits and giggles, let's package the responsive theme
    responsiveTheme = pkgs.stdenv.mkDerivation {
      name = "responsive-theme";
      # Download the theme from the wordpress site
      src = pkgs.fetchurl {
        url = http://wordpress.org/themes/download/responsive.1.9.7.6.zip;
        sha256 = "06i26xlc5kdnx903b1gfvnysx49fb4kh4pixn89qii3a30fgd8r8";
      };
      # We need unzip to build this package
      buildInputs = [ pkgs.unzip ];
      # Installing simply means copying all files to the output directory
      installPhase = "mkdir -p $out; cp -R * $out/";
    };

    # Wordpress plugin 'akismet' installation example
    akismetPlugin = pkgs.stdenv.mkDerivation {
      name = "akismet-plugin";
      # Download the theme from the wordpress site
      src = pkgs.fetchurl {
        url = https://downloads.wordpress.org/plugin/akismet.3.1.zip;
        sha256 = "1i4k7qyzna08822ncaz5l00wwxkwcdg4j9h3z2g0ay23q640pclg";
      };
      # We need unzip to build this package
      buildInputs = [ pkgs.unzip ];
      # Installing simply means copying all files to the output directory
      installPhase = "mkdir -p $out; cp -R * $out/";
    };

    in

    ...

      ######### <wordpress using httpd> #########################################
      # nix-env -qaP mysql
      services.mysql = {
        enable = true;
        package = pkgs.mysql;
      };

      services.httpd = {
        enable = true;
        logPerVirtualHost = true;
        adminAddr="js@lastlog.de";
        extraModules = [
          { name = "php5"; path = "${pkgs.php}/modules/libphp5.so"; }
        ];

        virtualHosts = [
          # webservice5 (http)
          {
            hostName = "webservice5";
            serverAliases = [ "webservice5" "www.webservice5" ];

            extraSubservices =
            [
              {
                serviceType = "wordpress";
                dbPassword = "wordpress";
                wordpressUploads = "/data/uploads";
                themes = [ responsiveTheme ];
                plugins = [ akismetPlugin ];
              }
            ];
          }
        ];
      };
      ######### </wordpress using httpd> ###########################