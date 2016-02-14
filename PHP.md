---
title: PHP
permalink: /PHP/
---

Installation
============

PHP can be installed with the following command:

` nix-env -i php`

The following command list available PHP versions:

` nix-env -qaP php`
` nixos.php54  php-5.4.44`
` nixos.php55  php-5.5.28`
` nixos.php    php-5.6.12`
` nixos.php70  php-7.0.0beta1`

Specific versions can be installed with:

` nix-env -i php54`

PHP Packages
============

PHP packages are in the **phpPackages** sub attribute and can be listed with:

` nix-env -f "`<nixpkgs>`" -qaP -A phpPackages`

Adding PHP packages
-------------------

### Custom Packages (Local)

It is recommended to read the [documentation](https://nixos.org/nixos/manual/sec-package-management.html#sec-custom-packages) for custom package management before adding a package.
**buildPecl** function can be used to easily create derivations for PHP packages.

Example for Imagick extension:

` with import `<nixpkgs>` {}; # bring all of Nixpkgs into scope`
` phpPackages.buildPecl {`
`   name = "imagick-3.1.2";`
`   sha256 = "14vclf2pqcgf3w8nzqbdw0b9v30q898344c84jdbw2sa62n6k1sj";`
`   configureFlags = "--with-imagick=${pkgs.imagemagick}";`
`   buildInputs = [ pkgconfig ];`
`   IMAGICK_AFTER_BWC_BREAK="false";`
`   WAND_BINARY="${pkgs.imagemagick}/bin/MagickWand-config";`
` }`

explanation:

-   **name** is used for package name, and for fetching the source. The package source url is **<http://pecl.php.net/get/$%7Bname%7D.tgz>**.
-   **sha256** is the hash of the source, it can be found with the **nix-prefetch-url** tool.
-   **configureFlags** are used when customizing build configuration is needed.
-   **buildInputs** is used to specify build time dependency.

### To nixpkgs

Be sure to read the [NixOS contributor guide](http://nixos.org/nixpkgs/manual/).

PHP packages are defined in [php-packages.nix](https://github.com/NixOS/nixpkgs/blob/master/pkgs/top-level/php-packages.nix).

Using with Apache
=================

To use PHP with apache, the following configuration can be added to configuration.nix:

` services.httpd = {`
`   enable = true;`
`   enablePHP = true;`
` }`

Specific versions of PHP can be used via packageOverrides, for example using PHP 5.4:

` nixpkgs.config = {`
`   packageOverrides = {`
`     php = pkgs.php54;`
`   };`
` };`
` `
` services.httpd = {`
`   enable = true;`
`   enablePHP = true;`
` }`

PHP extensions can be enabled with phpOptions:

``
`  phpOptions = ''`
`    extension=${pkgs.phpPackages.imagick}/lib/php/extensions/imagick.so`
`  '';`

Nix containers
--------------

Nix containers can also be used:

` containers.httpdphp = {`
`   privateNetwork = true;`
` `
`   hostAddress    = "192.168.101.10";`
`   localAddress   = "192.168.101.11";`
` `
`   config = { config, pkgs, ... }: { `
`     networking.firewall = {`
`       allowedTCPPorts = [ 80 443 ];`
`     };`
` `
`     services.httpd = {`
`       enable = true;`
`       enablePHP = true;`
`       adminAddr = "web1@example.org";`
`       documentRoot = "/srv/http";`
`     };`
`   };`
` };`

PHP Web-Apps
------------

Some popular PHP web-apps like wordpress or mediawiki are available as Apache sub-services.
Please refer to their respective wiki pages for more information:

-   [Wordpress](/Wordpress "wikilink")
-   [MediaWiki](/MediaWiki "wikilink")

PHP-FPM
=======

Setting services.phpfpm.poolConfigs in the global configuration will automatically enable and start the php-fpm systemd unit:

`   services.phpfpm.poolConfigs = {`
`      mypool = '' `
`        listen = 127.0.0.1:9000`
`        user = dev`
`        pm = dynamic`
`        pm.max_children = 75`
`        pm.start_servers = 10`
`        pm.min_spare_servers = 5`
`        pm.max_spare_servers = 20`
`        pm.max_requests = 500 `
`      '';`
`    };`

Settings
--------

Global PHP settings used by php-fpm can be specified with the following snippet:

`   services.phpfpm.phpIni = pkgs.runCommand "php.ini"`
`        { options = ''`
`            zend_extension=${pkgs.phpPackages.xdebug}/lib/php/extensions/xdebug.so`
`            max_execution_time = 30`
`          '';`
`        }`
`        ''`
`          cat ${pkgs.php}/etc/php-recommended.ini > $out`
`          echo "$options" >> $out`
`        '';`