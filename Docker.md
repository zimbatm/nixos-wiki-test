---
title: Docker
permalink: /Docker/
---

Docker on NixOS
---------------

-   Add `virtualisation.docker.enable = true;` to your `configuration.nix`
-   `$ gpasswd -a ielectric docker`

With this, the `docker` command will work for the user `ielectric` (as well as `root`).

Running nix-docker
------------------

Run this command as root:

`   nix-docker -p`

On a different shell:

`   nix-docker images`

Docker + Btrfs
--------------

To use Docker with btrfs you need to

-   configure a subvolume for Docker
-   mount it at /var/lib/storage
-   configure the Docker storage-driver for btrfs

This is fairly simple.

` sudo mkdir /mnt/rootfs`
` sudo mount -o subvolid=0 /dev/sdaX /mnt/rootfs`
` cd /mnt/rootfs`
` sudo btrfs subvolume create @docker`

Ensure this subvolume is in your /etc/nixos/hardware-configuration.nix, and that it gets mounted at /var/lib/storage.

**Please note this will effectively shadow and thus clear your existing Docker containers.**

` fileSystems."/var/lib/docker" =`
`   { device = "/dev/disk/by-uuid/a6772692-1005-4100-6542-00d245b55664";`
`     fsType = "btrfs";`
`     options = "subvol=@docker";`
`   };`

You should adjust your device to match the others in your configuration on the same btrfs volume.

After that ensure you have the following two lines in your /etc/nixos/configuration.nix file:

` virtualisation.docker.enable = true;`
` virtualisation.docker.storageDriver = "btrfs";`

(For ZFS and other options, see [docker.nix on Github](https://github.com/NixOS/nixpkgs-channels/blob/nixos-unstable/nixos/modules/virtualisation/docker.nix#L41-L49))

After this you will need to rebuild and reboot:

` sudo nixos-rebuild switch`
` reboot`

After creating your first docker container, you should now be able to see btrfs subvolumes created by Docker:

` $ sudo btrfs subvolume list /var/lib/docker`
` ...`
` ID 325 gen 12215 top level 321 path btrfs/subvolumes/9377ad319b00884df249b7820e3cf540b1c4631b3b1ee6998a0f7c3d53962e03`
` ID 326 gen 12216 top level 321 path btrfs/subvolumes/a82f81f257507f5cb74e833ff1ae4a6a39dfa654a161f5393f641832872b87d3`
` ID 327 gen 12217 top level 321 path btrfs/subvolumes/b207c06aba70227e0a2561bb7df20a5fd1310901da98ecc6f4da7dccdc40d961`
` ID 328 gen 12218 top level 321 path btrfs/subvolumes/d55e68e6cc9c7f78f1c02001e1a5ce76511db044c659e5c0a4275c54473f2869`
` ...`

If not, Docker is still not configured to use the btrfs storage-driver and you will need to debug your configuration further. You may consider looking into the following notes below, which holds additional options.

Conversation on \#docker channel about packaging Docker v0.10.0:

> ` `<chexxor>` backjlack, I included btrfsProgs as a build dependency, but it looks like the docker daemon can't use it, saying something like "/var/docker/X" is not a btrfs volume. Does the OS require some setup to use the AuFS or btrfs drivers?`
>
> ` `<backjlack>` chexxor: Yes, if you want to use btrfs, you need to create a btrfs partition and make Docker use it like this:`
> `     a) ln -s /btrfs/mountpoint/docker_storage /var/lib/docker (docker_storage must exist and /var/lib/docker must not exist)`
> `     OR`
> ``      b) start the Docker daemon with `-g /btrfs/mountpoint/docker_storage". ``
>
> ` `<backjlack>``  If that fails, you can set the graphdriver (storage driver) by hand with `-s btrfs -g /btrfs/mountpoint/docker_storage`. ``

Docker Error
------------

Binaries built on Nixos is using a dynamic linker that probably isn't available in your Docker image, and this will cause an error.

For instance to run a Haskell generated binary in Centos 7 use \[patchelf [1](https://nixos.org/patchelf.html)\] to change the linker like this:

`   patchelf --set-interpreter /lib64/ld-linux-x86-64.so.2 dist/build/myapp/myapp`