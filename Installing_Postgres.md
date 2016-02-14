---
title: Installing Postgres
permalink: /Installing_Postgres/
---

In order to install Postgres add the following lines to your *configuration.nix*:

` services.postgresql.enable = true;`
` services.postgresql.package = pkgs.postgresql94;`
` services.postgresql.authentication = "local all all ident";`

The first one will ensure the service gets started, the second one tells which version of Postgres to use. The default value of *authentication* is empty making it hard to log onto the service. This is changed by the third line: The locally logged on user is allowed to connect to Postgres as long as there is a corresponding user available in Postgres.

Apply the configuration using:

` $ nixos-rebuild switch`

Now we have to create a role in Postgres for your every-day-account. To do so we become the root user and connect to the Database *postgres*:

` $ su -`
` $ psql postgres`

To create the role we use:

` postgres=# CREATE ROLE yourUserNameHere SUPERUSER LOGIN;`
` postgres=# \q`

Now you will be able to connect locally without password. For other options please refer to the Official documentations at:

-   Postgres Module in the NixOS-Manual [1](http://nixos.org/nixos/manual/module-postgresql.html)
-   Official documentation [2](http://www.postgresql.org/docs/)
