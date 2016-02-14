---
title: NixOS Chaos Communication Camp 2015
permalink: /NixOS_Chaos_Communication_Camp_2015/
---

what is this? i want to have several NixOS related workshops at the Chaos Communication Camp 2015 so that ppl get into nixos. if you are there just come by and have a $mate with us and feel free to participate in our workshops.

the workshops cover a wide field of nixos related technologies!

who
---

if you want to join, just write your email below:

-   js@lastlog.de (qknight, organizer)
-   lassulus@gmail.com (lassulus, helper)
-   fpletz@fnordicwalking.de (fpletz)
-   moritz@tarn-vedra.de (the-kenny)

SEARCHING FOR HELPERS ;-)

THIS IS AWESOME!

where
-----

you can find the NixOS village near the 'shackspace' (which is probably part of the KESSEL-village).

workshops
---------

the workshops will be executed in the order shown here, dates and hours will be announced soon.

**we focus on newcomers and love to share our knowledge ;-)**

### workshop: nix programming

nixos features a linux based operating system on top of the programming language nix. in this workshop we will look into the nix programming language.

<https://nixos.org/nix/manual/>

### workshop: nix-shell

nix-shell is a handy util to create custom environments for development on the fly.

say you work on software which is not in nixpkgs but you still want to use nix to manage dependencies like libraries using nix on nixos/linux and other posix systems in your team.

then nix-shell comes in handy as it uses nixpkgs to manage libraries.

we will also have a look into myEnvFun which is very similar to nix-shell but features a persistent development environment which is not reevaluated every time you run:

    % nix-shell default.nix

<https://nixos.org/wiki/Development_Environments>

### workshop: nixpkgs

**in this workshop we will add new software into nixpkgs and learn the workflow on how to create a pull request to githbub.com/nixos/nixpkgs.** nixpkgs is the git repository where the nixos project stores all the software and its dependencies.

we can discuss what programming languages do have good support in nixpkgs already and which not. you can bring your own ideas/wishes with your, we will discuss it there.

<http://nixos.org/nixpkgs/manual/>

### workshop: hydra

nixos bases on 'source deployment' but provides binary downloads by so called: 'binary substitutes'. However, NixOS software is automatically built using the hydra software \[1\] at hydra.nixos.org so that users can download binaries instead of having to compile their software on their own.

in this workshop we will setup or own hydra in a virtual machine.

If you want to take part please let me know by email: js@lastlog.de so that i know how many people are interested in this workshop.

<https://github.com/NixOS/hydra>

### workshop: nixOps

**nixOps is a tool for deploying your set of computers**, read 'cloud', based on a local nixpkgs checkout and additional configuration.

we will also look into deploying multiple machines at one and doing rollbacks of our configuration.

among this we will learn how to make modifications to nixpkgs, like adding our own programs/tools.

<https://nixos.org/nixops/manual/>

### workshop: nixos unit tests

in nixos you can check things like LVM, luks, installer, quake3 performance, x-server comaptibility, and much more with automated tests which are executed inside a KVM environemnt.

<https://github.com/NixOS/nixpkgs/tree/master/nixos/tests>

links
-----

-   \[1\] <https://events.ccc.de/camp/2015/wiki/Main_Page>
-   \[2\] <http://events.ccc.de/tag/chaos-communication-camp/>
