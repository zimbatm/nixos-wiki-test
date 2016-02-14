---
title: Installing NixOS on GCE
permalink: /Installing_NixOS_on_GCE/
---

This is a recipe for creating a NixOS machine on Google Compute Engine (GCE) which is part of [Google Cloud Platfrom](https://cloud.google.com/). This tutorial assumes you have already set up and account and project under Google Cloud Platform.

Preparing a NixOS image
-----------------------

To bootstrap the process, first start any Linux instance and log into it.

1.  From the Google Developers Console select Compute - Compute Engine - VM instances
2.  Press "Create an Instance" or "New instance" button
3.  Click on "Show advanced options"
4.  Select a ZONE close to you and any MACHINE TYPE (f1-micro is adequate)
5.  Select any linux image such as centos-7
6.  Make sure "Enable Compute Engine service account." is checked
7.  Under COMPUTE select "Read Write"
8.  Press "Create" button and wait until the VM is ready
9.  From the Google Developers Console select Compute - Compute Engine - VM instances press the "SSH" button on your new instance (usually named "instance-1") to log in

Next we will create a NixOS image. A source for NixOS images can be found the google storage bucket named [`gs://nixos-images`](https://storage.cloud.google.com/nixos-images).

    $ gsutil ls -l gs://nixos-images
     256556736  2014-12-17T10:51:00Z  gs://nixos-images/nixos-14.10pre-git-x86_64-linux.raw.tar.gz
     290985235  2014-12-19T12:45:58Z  gs://nixos-images/nixos-14.12.542.4c9ef9f7-x86_64-linux.raw.tar.gz

In this tutorial we will use `gs://nixos-images/nixos-14.12.542.4c9ef9f7-x86_64-linux.raw.tar.gz` and follow the [documentation on how to create an image from a `.tar.gz` file](https://cloud.google.com/compute/docs/images?_ga=1.122328651.1179090775.1417532639#creating_an_image_from_a_tar_file).

    $ gcloud compute images create nixos-14125424c9ef9f7-x86-64-linux --source-uri gs://nixos-
    images/nixos-14.12.542.4c9ef9f7-x86_64-linux.raw.tar.gz
    $ gcloud compute images describe nixos-14125424c9ef9f7-x86-64-linux
    ...
    status: READY

You can now log out of your VM and delete the instance

1.  `$ logout`
2.  From the Google Developers Console select Compute - Compute Engine - VM instances check your instance and then press the "Delete" button at the top of the page.

Starting a NixOS image
----------------------

Now that the image is made we can start as many NixOS VMs as we like.

### (Strongly Recommended) Generate SSH host keys

Before beginning we should generate unique SSH host keys for our new VM so that when will be able to authenticate our SSH connection. The example below generates keys `/dev/shm/ssh_host_ecdsa_key` and `/dev/shm/ssh_host_ecdsa_key.pub` files.

    $ ssh-keygen -N '' -C '' -t ecdsa -f /dev/shm/ssh_host_ecdsa_key
    Generating public/private ecdsa key pair.
    Your identification has been saved in /dev/shm/ssh_host_ecdsa_key.
    Your public key has been saved in /dev/shm/ssh_host_ecdsa_key.pub.
    The key fingerprint is:
    92:2a:e9:28:1a:cd:43:71:31:36:f2:8e:6e:fa:13:c4
    The key's randomart image is:
    +--[ECDSA  256]---+
    |  . =            |
    |   + +           |
    | .. o            |
    |  E=   .         |
    | .o . o S        |
    | =.. . .         |
    |. O..            |
    |.*.o             |
    |*.o.             |
    +-----------------+

**Take note of your unique key fingerprint.**

### Create your VM

Now we are ready to create a NixOS VM.

1.  From the Google Developers Console select Compute - Compute Engine - VM instances
2.  Press "Create an Instance" or "New instance" button
3.  Click on "Show advanced options"
4.  (Strongly Recommended) Under METADATA, create a Key called `ssh_host_ecdsa_key` and copy the contents of your `/dev/shm/ssh_host_ecdsa_key` to the corresponding Value if you generated SSH host keys.
5.  (Strongly Recommended) Under METADATA, create a Key called `ssh_host_ecdsa_key_pub` and copy the contents of your `/dev/shm/ssh_host_ecdsa_key.pub` to the corresponding Value if you generated SSH host keys.
6.  Under SSH KEYS, copy the contents of your desktop computer's `~/.ssh/id_rsa.pub` file into the box that says "Enter entire key data"
7.  Under BOOT SOURCE, make sure "New disk form image" is selected
8.  Under IMAGE, select your nixos image, e.g. "nixos-14125424c9ef9f7-x86-64-linux".
9.  Set any additional options as you see fit.
10. Press the "Create" button and wait until the VM is ready

### Connect to your VM

Notwithstanding the username associated with your SSH, you must log in as root to the NixOS machine. Also, you will need to ssh in from your computer's terminal. Below replace `XXX.XXX.XXX.XXX` with the EXTERNAL IP address listed for your VM instance. (Strongly Recommended) Verify the ECDSA key fingerprint is the same as the one you generated.

    $ ssh -o UserKnownHostsFile=/dev/null -o CheckHostIP=no -o StrictHostKeyChecking=ask root@XXX.XXX.XXX.XXX
    The authenticity of host '130.211.149.218 (130.211.149.218)' can't be established.
    ECDSA key fingerprint is 92:2a:e9:28:1a:cd:43:71:31:36:f2:8e:6e:fa:13:c4.
    Are you sure you want to continue connecting (yes/no)? yes
    Enter passphrase for key '.../.ssh/id_rsa':

    [root@instance-1:~]#

Once you are logged into your NixOS machine, I recommend that you create an user account for yourself with administrator privileges.

1.  `chmod u+w /etc/nixos/configuration.nix`
2.  `nano -w /etc/nixos/configuration.nix`
3.  Add the follow to the configuration:

<!-- -->

      security.sudo.wheelNeedsPassword = false;
      users.extraUsers.<your-username> = {
        createHome = true;
        home = "/home/<your-username>";
        description = "<your-name>";
        group = "users";
        extraGroups = [ "wheel" ];
        useDefaultShell = true;
        openssh.authorizedKeys.keys = [ "<contents of your ~/.ssh/id_rsa.pub>" ];
      };

After you save this file run `nixos-rebuild switch --upgrade`. Once that is complete reboot and log back in with your user account. (Strongly Recommended) Again verify the ECDSA key fingerprint is the same as the one you generated. If you plan to keep this instance running for a long time you may removed the `-o UserKnownHostsFile=/dev/null` option to SSH to add the host to your `~/.ssh/known_hosts` file, however be advised that IP addresses for GCE instances are often reused, so when you create and destroy instances you may end up with stale keys in your `known_hosts` file.

    $ ssh -o UserKnownHostsFile=/dev/null -o CheckHostIP=no -o StrictHostKeyChecking=ask <your-username>@XXX.XXX.XXX.XXX
    The authenticity of host '130.211.149.218 (130.211.149.218)' can't be established.
    ECDSA key fingerprint is 92:2a:e9:28:1a:cd:43:71:31:36:f2:8e:6e:fa:13:c4.
    Are you sure you want to continue connecting (yes/no)? yes
    Enter passphrase for key '.../.ssh/id_rsa':

    [<your-username>@instance-1:~]$

At this point you may want to snapshot this image and use this snapshot to make future VMs. You should also delete the `/dev/shm/ssh_host_ecdsa_key` and `/dev/shm/ssh_host_ecdsa_key.pub` files from your home machine if you generated them.