---
title: Quassel
permalink: /Quassel/
---

From the [homepage](http://quassel-irc.org/):

> Quassel IRC is a modern, cross-platform, distributed IRC client, meaning that one (or multiple) client(s) can attach to and detach from a central core -- much like the popular combination of screen and a text-based IRC client such as WeeChat, but graphical. In addition to this unique feature, we aim to bring a pleasurable, comfortable chatting experience to all major platforms (including Linux®, Windows®, and MacOS X® as well as Android smartphones), making communication with your peers not only convenient, but also ubiquitous available.

Client
------

Several versions of the Quassel client are available in Nixpkgs:

`kde4.quasselClient`
based on Qt 4, with KDE 4 integration

`kde4.quasselClientWithoutKDE`
based on Qt 4, no KDE integration

`quasselClient_kf5`
based on Qt 5, with KDE 5 integration

`quasselClient_qt5`
based on Qt 5, no KDE integration

For KDE 4 users, `kde4.quasselClient` is recommended. For all other users, `quasselClient_kf5` is recommended. The client may be installed with `nix-env` or it may be added to the `environment.systemPackages` NixOS option.

Server
------

To enable the Quassel server (variously referred to as the Quassel daemon or Quassel Core), add

    services.quassel.enable = true;

to your NixOS configuration. By default, the server only accepts connections from the local machine; set

    services.quassel.interface = "0.0.0.0";
    networking.firewall.allowedTCPPorts = [ 4242 ];

to accept external connections. At this point, you can activate the configuration with `nixos-rebuild switch`. The first time you connect to the new server, a wizard will guide you through the remaining configuration steps. Before connecting to the new server, you may wish to optionally configure SSL or PostgreSQL.

### SSL

The Quassel server can use SSL if a certificate is provided. Install `openssl` (using `nix-env` or by adding it to `environment.systemPackages`) and run

    sudo -u quassel mkdir -p /home/quassel/.config/quassel-irc.org
    sudo -u quassel openssl req -x509 -nodes -days 365 -newkey rsa:4096 -keyout /home/quassel/.config/quassel-irc.org/quasselCert.pem -out /home/quassel/.config/quassel-irc.org/quasselCert.pem

You will be prompted for some details about the certificate. If `quassel.service` was running, restart it by running:

    sudo systemctl restart quassel.service

### PostgreSQL

By default, Quassel uses an SQLite database, but it can be configured to use PostgreSQL for better performance. (If you aren't sure which database backend you want to use, stay with SQLite because it is always possible to switch to PostgreSQL later.)

Start by enabling the PostgreSQL service in your NixOS configuration:

    /* Enable the PostgreSQL service */
    services.postgresql = {
      enable = true;
      package = pkgs.postgresql94;
    };

    /* Only start Quassel after PostgreSQL has started */
    systemd.services.quassel.after = [ "postgresql.service" ];

    /* Make the quasselcore command available in the shell */
    environment.systemPackages = [ pkgs.quasselDaemon_qt5 ];

Activate this configuration with `nixos-rebuild switch`. Create a PostgreSQL role (user) for Quassel:

    sudo createuser -A -D -P -E -W quassel

You will be prompted for a password; you will also need to provide the password to Quassel, so don't forget it! Create a PostgreSQL database for Quassel:

    sudo createdb -O quassel -E UTF8 quassel

If you have never connected to this Quassel server before, you can connect now and select the PostgreSQL backend when prompted. You will need to provide the PostgreSQL username (`quassel`) and password you created above.

If you are switching from the SQLite backend to the PostgreSQL backend, you will need to migrate the existing data. Stop the Quassel server with

    sudo systemctl stop quassel.service

Start the migration with

    sudo -u quassel quasselcore --configdir=/home/quassel/.config/quassel-irc.org --select-backend=PostgreSQL

You will need to provide the PostgreSQL username (`quassel`) and password you created above. When this is finished, restart the Quassel server:

    sudo systemctl start quassel.service