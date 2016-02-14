---
title: NixOS:Containers
permalink: /NixOS:Containers/
---

Containers can be a very handy thing. This page is dedicated to the beginner and teaches how to use containers with nixos. First, we want to create a new container (on this page "test" is the container name):

`   $ sudo nixos-container create test`

Which can now be started and stopped with the following commands:

`   $ sudo nixos-container start test`
`   $ sudo nixos-container stop test`

You can check each containers status with the following command:

`   $ sudo nixos-container status test`
`   # or`
`   $ systemctl status container@test`

Creating a user
---------------

Of course, we want to add a normal user in the container now. And, of course, we want to do this in the nixos-way: Via a configuration file. The configuration file of our new container can be edited from the "host" system:

`   $ sudo vim /var/lib/containers/test/etc/nixos/configuration.nix`

And we enter the following lines to install "vim", "git" and "htop" and setup a new user (called "user") inside the container:

`   { config, lib, pkgs, ... }:`
`   `
`   with lib;`
`   `
`   { boot.isContainer = true;`
`     networking.hostName = mkDefault "gitit";`
`     networking.useDHCP = false;`
`   `
`     environment.systemPackages = with pkgs; [`
`        vim`
`        git`
`        htop`
`     ];`
`   `
`     users.extraUsers.user = {`
`        name = "user";`
`        group = "users";`
`        uid = 1000;`
`        createHome = true;`
`        home = "/home/user";`
`        extraGroups = [ "users" "wheel" ];`
`        shell = "/var/run/current-system/sw/bin/bash";`
`        isNormalUser = true;`
`     };`
`   `
`   }`

Now we start the container again and we can login with the root user to set the users password for the "user" user:

`   $ sudo nixos-container root-login test`
`   [root@test:~]# passwd user`
`   Enter new UNIX password: `
`   Retype new UNIX password: `
`   passwd: password updated successfully`
`   [root@test:~]# exit`
`       `
`   $ sudo nixos-container login test`
`   Connected to container test. Press ^] three times within 1s to exit session.`
`   `
`   <<< Welcome to NixOS 16.03pre68239.bd84eba (x86_64) - pts/0 >>>`
`   `
`   test login: `

Where we enter the username "user" and the password we have set with the root user.

Now we have a fully operational container with nixos running inside and we can do things without affecting the main system at all.