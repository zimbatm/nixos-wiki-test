---
title: Install remove software
permalink: /Install/remove_software/
---

How to install software
-----------------------

There are (at least) four distinct ways to install software, with different purposes. These are

-   Installing into the global system environment
-   Installing into the root user profile
-   Installing into a normal user profile
-   Installing into a project/program-specific environment (e.g. with nix-shell or myEnvFun)

### System environment installation

This method makes software globally available for all users, and is the closest to functionality such as *apt-get install* in Debian/Ubuntu. This option is only available if you are running a full NixOS installation, as opposed to standalone Nix.

:\* Note: This method is preferred over the next method (root user profile) due to potential issues with kernel modules \[TODO: needs more clarification\].

To install a package to the system environment, add it to the corresponding *environment.systemPackages* list in */etc/nixos/configuration.nix*

` environment.systemPackages = with pkgs; [`
`   zsh wget`
` ];`

and then run:

` nixos-rebuild switch`

Removing software installed into the system environment is almost the same process - remove the package from *environment.systemPackages*, then run *nixos-rebuild switch*

### Root user installation

Software installed via nix-env as root is also globally available to all users, e.g.

` nix-env -i links2`

:\* Note: generally the method above (system environment) is preferable to this. However this method could be useful if you have a standalone Nix installation (in which case the method above is not available).

To remove software installed in this way, use nix-env as the root user

` nix-env -e links2`

### Normal user installation

This method makes software available only for the user who installs it. Take care not to use sudo, or else you have hit the case above (root user installation)

`nix-env -i links2`

To remove software installed in this way, use nix-env

` nix-env -e links2`

### Installing into a subshell

In some cases, you may want access to a program for a specific task or project, but do not want to install it into your user or system profiles. For example, if you have five projects under development, each of which depends on a different version of the Python interpreter, then you can constrain each interpreter to be available only within the context of the corresponding project, instead of trying to install them into the system/user profiles. For more information see <https://nixos.org/wiki/Develop_software_with_unique_profiles_using_Nix>

Garbage collection
------------------

Removing software as described above will not immediately free up disk space. This is because previous generations of profiles are maintained to enable rollbacks. To free up disk space run

` nix-env --delete-generations old`
` nix-collect-garbage`

:\* Note: *nix-env --delete-generations old* should be run as the same user (root or normal user) from whose profile you uninstalled software

:\* Note: as a shortcut to garbage collect everything not currently used by some profile, run as root

` nix-collect-garbage -d`

:\* Note: after garbage-collecting old generations, rolling back to them is no longer possible

How to list installed software
------------------------------

### Software per user

That works for each 'shell' of a single user:

` nix-env -q`

### Software in the environment.systemPackages

There is no command to list that at this time (8jun2011)

But there is this workaround (tnx to niksnut!):

` nix-store -q --references /var/run/current-system/sw`

Run this to see package names only:

` nix-store -q --references /var/run/current-system/sw | cut -d'-' -f2-`

How to update installed software
--------------------------------

### Software per user

` nix-env -u --keep-going`

If you want to update even SW that haven't changed version but something did change (deps. etc.), you need to add --leq.

### Software in the environment.systemPackages

If you have updated your channel with

` nix-channel --update`

Or updated them manually, by hand, or pulling updates from a source control system then you can use:

` nixos-rebuild switch`

To update any installed packages with updated nix expressions.

Or you can use :

` nixos-rebuild switch --upgrade`

How to roll back software
-------------------------

To undo the last

` nixos-rebuild switch`

You can

` nixos-rebuild --rollback`

Note this does not revert configuration files like /etc/nixos/configuration.nix to their previous states so if you do not change them by hand the next "nixos-rebuild switch" will install them again.

To see the available system generations you can pass the profile to nix-env:

` nix-env -p /nix/var/nix/profiles/system --list-generations`

To rollback the system to an earlier generation:

` nix-env --switch-generation 3141 -p /nix/var/nix/profiles/system`
` /nix/var/nix/profiles/system/bin/switch-to-configuration switch`

For per-user packages.

` nix-env --rollback`

Also see nix-env --list-generations, which will list out the number of previous generations and the time stamp of their creation.

Manage nix-build/nix-shell
--------------------------

In /nix/var/nix/gcroots/auto there are links like these:

`   lrwxrwxrwx 1 root root     74 Jul  3  2013 182qrbs2zqkm6g4waqw4xl7pc484zyss -> /home/joachim/Desktop/projects/nixos/nixpkgs/pkgs/desktops/kde-4.10/result/`
`   lrwxrwxrwx 1 root root     12 Feb  1 18:23 7h9kz6xpi43rhva1alqny0hi54w6203b -> /root/result`
`   lrwxrwxrwx 1 root root     45 Jul 31  2013 9355idpn9cxg4xfxgwwjvrdlg481gr0s -> /home/joachim/Desktop/projects/nix-ray/result/`
`   lrwxrwxrwx 1 root root     47 Mar 20 00:15 964c6j34pd3b6qm418gc4j2pmwj890l7 -> /home/joachim/Desktop/projects/nixos/nix/result/`
`   lrwxrwxrwx 1 root root     51 Mar 20 00:50 bwj2bawqgg8v2hw41h7zyidky2wsynar -> /home/joachim/Desktop/projects/nixos/nixpkgs/result/`
`   lrwxrwxrwx 1 root root     32 May 12 16:19 mh0rdyn6ldcdljjzwpfzgfhmwyl7hi51 -> /tmp/nix-build.Jyenoc/derivation`
`   lrwxrwxrwx 1 root root     30 Aug 20  2013 q1s5wwxrnn6dc0g856vxz22lfad4gg6h -> /home/joachim/Downloads/result/`

You can remove all, which you do not need anymore BUT except this one:

`   lrwxrwxrwx 1 root root     43 Jun 12  2012 77pq4kkwkcsbxczg1d3hhvi4n33la519 -> /nix/var/nix/gcroots/per-user/root/channels/`

Afterwards run:

`   nix-store --gc`

Repair your installation
------------------------

Fix corrupted or missing store paths by redownloading or rebuilding them. Note that this is slow because it requires computing a cryptographic hash of the contents of every path in the closure of the build.

` $ export NIX_REMOTE="" `
` $ nix-store --verify --check-contents --repair `

Note: both commands need to be run as root

If these command didn't help, it may be that

` $ nix-store --delete $(nix-store --verify --check-contents)`

will help. It lists every corrupted path and removes it together with all reverse dependencies. This can fail if some of corrupted files are still used by some GC roots.

nix-channels
------------

NixOS channels are now the default mechanism for keeping NixOS up to date. (A channel is a Nix mechanism for distributing a consistent set of Nix expressions and binaries.)

A quick summary on how to use it ([official docs](http://nixos.org/nixos/manual/#sec-upgrading)):

` $ nix-channel --add `[`http://nixos.org/channels/`](http://nixos.org/channels/)*`name`*` nixos`
` $ nix-channel --update`
` $ nixos-rebuild switch`

Currently there are nixos and nixpkgs unstable channels, and release-13.10 channel which only receives security+maintenance updates.

The NixOS channel contains a copy of Nixpkgs, so after "nix-channel --update" you can install packages via nix-env:

` $ nix-env -i thunderbird`

or, if you prefer the "-A" option:

` $ nix-env -iA nixos.pkgs.thunderbird`

The advantages of using a channel over the previous way (doing "svn up /etc/nixos/\*" a.k.a. nixos-checkout) are:

-   Correctness: The channel is only updated to a new revision of NixOS/Nixpkgs if it passes certain tests - specifically, if the "tested" job succeeds (http://hydra.nixos.org/job/nixos/trunk-combined/tested). So you have a reasonable certainty that the new version will actually work. By contrast, if you update to the latest revision using nixos-checkout, it's quite possible that somebody just committed something that broke the entire system.

<!-- -->

-   Efficiency: The channel is only updated after all Hydra builds in that revision have finished, \*and\* all binaries have been mirrored. So you don't have to worry about somebody just having committed something that causes a huge recompilation.

This makes keeping NixOS up to date a rather more pleasant activity, IMHO (Eelco Dolstra).

Some things to note:

-   The downloaded channel sources are linked from /nix/var/nix/profiles/per-user/root/channels/nixos (which is actually in the Nix store). The $NIX_PATH environment variable contains this directory, so <nixos> and <nixpkgs> in Nix expressions resolve there unless overridden through $NIX_PATH or the "-I" flag.

<!-- -->

-   In principle, you can do rollbacks on the channels profile ("nix-env -p /nix/var/nix/profiles/per-user/root/channels --rollback") if you don't like the result of a "nix-channel --update".

<!-- -->

-   The NixOS installation CD no longer contains or installs /etc/nixos/{nixos,nixpkgs} but instead uses the NixOS channel. You can of course run "nixos-checkout" if you want a working tree for development. (In other news, "nixos-checkout" now accepts a argument to specify the desired location of the working trees, e.g. "nixos-checkout /home/eelco/Dev". The default location /etc/nixos may eventually go away since it's a weird location for keeping large source trees.)

<!-- -->

-   To use a development tree in nixos-rebuild, just use the -I flag, e.g. "nixos-rebuild switch -I /home/eelco/Dev".

<!-- -->

-   The "nixpkgs_sys" link in ~/.nix-defexpr is no longer created. Instead you can use "nixos.pkgs" in "nix-env -iA" calls, as shown above. "nixos.pkgs" is the Nixpkgs in the NixOS channel, so it's guaranteed to have binaries available.

<!-- -->

-   The command "nixos-version" prints out the NixOS version you're currently running, e.g. "14.02pre-1dd0e05 (Baboon)". The version string include the NixOS and Nixpkgs revisions, respectively.

### nix-channels advanced usage

often you would use *nix-channel --update* and maybe *nixos-rebuild boot* so that the newly built system is activated after the next reboot. but say you wanted to change a very minimal setting in your current system before the next boot, like:

-   network settings (dhcp vs static ip)
-   install a service (like httpd)
-   or anything similar

which should affect the currently executed system profile, then you have to revert to the nix-channel version you were using to build the current system profile.

first run *nixos-version*

`   nixos-version`
`   14.11pre52840.c347f1c (Caterpillar)`

*nixos-version* is a simple script from '/run/current-system/sw/bin/nixos-version'. note down the version 'pre52840.c347f1c' and then do this:

``    for i in `find -L /nix/var/nix/profiles/per-user/root -name .version-suffix`; do echo "$i"; echo -n "   "; cat $i; echo ""; done ``
`   /nix/var/nix/profiles/per-user/root/channels-123-link/nixos/nixpkgs/.version-suffix`
`      pre49475.74f6be0`
`   /nix/var/nix/profiles/per-user/root/channels-121-link/nixos/nixpkgs/.version-suffix`
`      pre48350.41cd2d8`
`   /nix/var/nix/profiles/per-user/root/channels-125-link/nixos/nixpkgs/.version-suffix`
`      pre51468.2c0cc6c`
`   /nix/var/nix/profiles/per-user/root/channels-128-link/nixos/nixpkgs/.version-suffix`
`      pre52840.c347f1c`
`   /nix/var/nix/profiles/per-user/root/channels-122-link/nixos/nixpkgs/.version-suffix`
`      pre48350.41cd2d8`
`   /nix/var/nix/profiles/per-user/root/channels-129-link/nixos/nixpkgs/.version-suffix`
`      pre54658.6221af5`
`   /nix/var/nix/profiles/per-user/root/channels-127-link/nixos/nixpkgs/.version-suffix`
`      pre52727.5d97886`
`   /nix/var/nix/profiles/per-user/root/channels-126-link/nixos/nixpkgs/.version-suffix`
`      pre52020.7bc3005`
`   /nix/var/nix/profiles/per-user/root/channels/nixos/nixpkgs/.version-suffix`
`      pre54658.6221af5`
`   /nix/var/nix/profiles/per-user/root/channels-124-link/nixos/nixpkgs/.version-suffix`
`      pre50818.b5fe0d3`

as it turns out, the link we were searching for was:

`   /nix/var/nix/profiles/per-user/root/channels-128-link/nixos/nixpkgs/.version-suffix`
`      pre52840.c347f1c`

finally activate 128:

`   nix-env -p /nix/var/nix/profiles/per-user/root/channels --list-generations`
`   nix-env -p /nix/var/nix/profiles/per-user/root/channels -G 128`

and finally you can affect your current system profile with this:

`   nixos-rebuild switch`

**note:** the previous *nixos-rebuild boot* will still listed be in GRUB but your default nix-channel is now poiting to 128 (and not 129) so if you want to make updates you have to run *nix-channel --update* again.

**note:** also beware of the garbage collector: running *nix-collect-garbage -d* will cause everything not currently in use to be removed, after which even a small change to your config may require rebuilding/redownloading a large number of packages which had previously been cached.

How to change the used kernel version
-------------------------------------

boot.kernelPackages \# see <http://hydra.nixos.org/job/nixos/trunk/manual/latest/download>

For example: add

` boot.kernelPackages = pkgs.linuxPackages_2_6_25;`

to your /etc/nixos/configuration.nix

Or check for more recent entries by:

` nix-env -qa | grep linux-2.6.`

How to use a different version of a package
-------------------------------------------

### KDE

1. Look into pkgs/top-level/all-packages.nix (when you have checked out nixpkgs using git) and try to find something like this:

` kde4 = recurseIntoAttrs pkgs.kde47;`
` kde47 = kdePackagesFor pkgs.kde47 "4.7";`
` kde410 = kdePackagesFor pkgs.kde410 "4.10";`

2. Now pick one of the versions by adding the lines below to /etc/nixos/configuration.nix:

` nixpkgs.config.packageOverrides = pkgs : rec { `
`   kde4 = pkgs.kde410; `
` };`

3. Install all kde stuff into the envinronment.systemPackages

` environment.systemPackages = with pkgs; [`
`   zsh`
`   linuxPackages.virtualbox`
`   wget`
`   kde4.kdemultimedia`
`   kde4.kdegraphics`
`   kde4.kdeutils`
`   kde4.applications`
`   kde4.kdegames`
`   kde4.kdeedu`
`   kde4.kdebindings`
`   kde4.kdeaccessibility`
`   #kde4.kde_baseapps`
`   kde4.kactivities`
`   kde4.kdeadmin`
`   kde4.kdeartwork`
`   #kde4.kde_base_artwork`
`   kde4.kdenetwork`
`   kde4.kdepim`
`   kde4.kdepimlibs`
`   kde4.kdeplasma_addons`
`   kde4.kdesdk`
`   kde4.kdetoys`
`   #kde4.kde_wallpapers`
`   kde4.kdewebdev`
`   #kde4.oxygen_icons`
`   kde4.kdebase_workspace`
`   kde4.kdelibs`
`   kde4.kdevelop`
`   kde4.kdevplatform`
` ];`

4. Finally do:

` nixos-rebuild switch`

### GRUB

1. Similar to the kde example search the all-packages.nix for available grub versions

2. To use a different version of grub add this line to /etc/nixos/configuration.nix

` nixpkgs.config.packageOverrides = pkgs : rec { grub2 = pkgs.grub198; };`

[Category:Installation](/Category:Installation "wikilink") [Category:Nixpkgs](/Category:Nixpkgs "wikilink")