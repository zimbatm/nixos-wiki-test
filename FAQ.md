---
title: FAQ
permalink: /FAQ/
---

Frequently asked questions and common newcomer trouble should be put here so that we can point to this page instead of answering the same question over and over again.

New questions should go to <http://unix.stackexchange.com/questions/tagged/nixos>

Why is Nix written in C++ and Perl rather than a functional language like Haskell or OCaml, given the strong influence that functional programming has obviously had on Nix
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Mainly because Nix is intended to be lightweight, easy to learn and portable (zero dependencies).

Using nix to install new software requires downloading the source from upstream and the source has moved and therefore the download doesn't work
------------------------------------------------------------------------------------------------------------------------------------------------

After some google search with [myfile-0.1.2.tar.gz "index of"](https://www.google.com/search?q=myfile-0.1.2.tar.gz+%22index+of%22) one usually can find the file and either:

-   download it manually and copy into /tmp, then move that file into the store using:

` nix-prefetch-url `[`file:///tmp/myfile-0.1.2.tar.gz`](file:///tmp/myfile-0.1.2.tar.gz)` # if the file is in the /tmp directory`

-   download it directly

` nix-prefetch-url "`[`http://example.com/programs/foo/myfile-0.1.2.tar.gz`](http://example.com/programs/foo/myfile-0.1.2.tar.gz)`"`

How to restart a systemd service?
---------------------------------

` systemctl restart unit`

A list of services can be found with:

` systemctl list-units`

How to keep build-time dependencies around / be able to rebuild while being offline?
------------------------------------------------------------------------------------

configuration.nix:

` nix.extraOptions = ''`
`    gc-keep-outputs = true`
`    gc-keep-derivations = true`
`  '';`

Check 'man configuration.nix' for these options. Rebuild for these options to take effect:

` # nixos-rebuild switch`

List all store paths that form the system closure and realise them:

` $ nix-store -qR $(nix-instantiate /etc/nixos/nixos -A system) | xargs nix-store -r`
``  warning: you did not specify `--add-root'; the result might be removed by the garbage collector ``
` ...`
` `<build output and list of successfully realised paths>

Repeat for your user and further profiles:

` $ nix-store -qR ~/.nix-profile |xargs nix-store -r`

The warning can be ignored for profiles that are listed/linked in /nix/var/nix/profiles/ or one of its subdirectories.

Consult man pages of nix-store and nix-instantiate for further information.

How to add a custom udev rule in nixos?
---------------------------------------

Add something like this to your `/etc/nixos/configuration.nix` file:

` services.udev.extraRules = ''`
`    ATTRS{idVendor}=="0665", ATTRS{idProduct}=="5161", MODE="666", SYMLINK+="hidraw_protecthome"`
`  '';`

Why <hash>-<name> instead of <name>-<hash>?
-------------------------------------------

For the rare cases where we have to dig into the /nix/store it is more practical to keep in mind the first few letters at the beginning than finding a package by name. In addition, the hash is printed by Nix commands. If you still wonder why, run "`ls -1 /nix/store | sort -R -t - -k 2 | less`" in your shell.

This is what might happen if you don't garbage collect frequently, or if you are testing compilation variants:

` q0yi2nr8i60gm2zap46ryysydd2nhzhp-automake-1.11.1/`
` vbi4vwwidvd6kklq2kc0kx3nniwa3acl-automake-1.11.1/`
` wjgzir57hcbzrq3mcgxiwkyiqss3r4aq-automake-1.11.1/`
` 1ch5549xnck37gg2w5fh1jgk6lkpq5mc-nixos-build-vms/`
` 4cmjlxknzlvcdmfwj0ih0ggqsj5q73hb-nixos-build-vms/`
` 7fv4kwi5wwwzd11ili3qwg28xrj8rxw2-nixos-build-vms/`
` 8jij13smq9kdlqv96hm7y8xmbh2c54iy-nixos-build-vms/`
` j714mv53xi2j4ab4g2i08knqr137fd6l-nixos-build-vms/`
` xvs7y09jf7j48p6l0p87iypgpq470jqw-nixos-build-vms/`

Error: unable to check \`https://nixos.org/channels/nixpkgs-unstable\`
----------------------------------------------------------------------

**Note: this problem appears to have already been fixed as of Nov 2014.**

If you get an error like this: /home/user/.nix-profile/bin/nix-channel: unable to check \`https://nixos.org/channels/nixpkgs-unstable'

It's because of the https protocol: change the protocol to be http. For more information see <https://github.com/NixOS/nixpkgs/pull/2688>

Another possible cause for this behaviour is a misconfigured shell profile. During installation nix adds the line

` if [ -e /home/`<user>`/.nix-profile/etc/profile.d/nix.sh ]; then . /home/`<user>`/.nix-profile/etc/profile.d/nix.sh; fi`

to *~/.profile*. If your shell does not source this file, add the line to your shell configuration (e.g. to .zshrc).

I've updated my channel and something is broken, how can I rollback to an earlier channel?
------------------------------------------------------------------------------------------

View the available generations of your channel:

` ``
  $ nix-env --list-generations -p /nix/var/nix/profiles/per-user/root/channels
    18   2014-04-17 09:16:28
    19   2014-06-13 10:31:24
    20   2014-08-12 19:09:20   (current)
  `

To rollback to the previous generation:

` ``
  $ nix-env --rollback -p /nix/var/nix/profiles/per-user/root/channels
  switching from generation 20 to 19
  `

To switch to a particular generation:

` ``
  $ nix-env --switch-generation 18 -p /nix/var/nix/profiles/per-user/root/channels
  switching from generation 20 to 18
  `

I'm working on a new package, how can I build it without adding it to nixpkgs?
------------------------------------------------------------------------------

` ``
  $ nix-build -E 'with import <nixpkgs> { }; callPackage ./mypackage.nix { }'
  `

You can replace callPackage with callPackage_i686 to build the 32-bit version of your package on a 64-bit system if you want to test that.

How can I compile a package with debugging symbols included?
------------------------------------------------------------

To build a package with -O0 and -g, and without stripping debug symbols use:

` ``
  $ nix-build -E 'with import <nixpkgs> { }; enableDebugging fooPackage'
  `

How can I force a rebuild from source even without modifying the nix expression?
--------------------------------------------------------------------------------

As root you can run nix-build with the --check flag:

` ``
  /root/nixpkgs $ nix-build --check -A ncdu
  `

How can I manage software with nix-env like with configuration.nix?
-------------------------------------------------------------------

There are many ways, one is the following:

1) Create your `packages.nix` file with the packages you would like to have in your environment:

` ``
  with (import <nixpkgs> {});
  { inherit pkg1 pkg2 ....; }
  `

2) Install all specified packages using this command:

`  ``$ nix-env -f packages.nix -i`

With this approach, you can also choose to install only certain packages by specifying their attribute name like this:

` ``$ nix-env -f packages.nix -iA pkg1`

3) Install all the packages, and remove everything else by using the --remove-all (-r) switch

` ``$ nix-env -f packages.nix -ir`

I've downloaded a binary, but I can't run it, what can I do?
------------------------------------------------------------

It probably just needs to know where to find the libraries it needs. You can write a simpler wrapper which sets LD_LIBRARY_PATH appropriately and installs it:

mybinaryprogram.nix:

      with import <nixpkgs> {}; with xlibs;

      stdenv.mkDerivation rec {
          name = "somename";
          buildInputs = [ makeWrapper ];

          buildPhase = "true";

          libPath = lib.makeLibraryPath [ libXrandr libXinerama libXcursor ];

          unpackPhase = "true";

          installPhase = ''
            mkdir -p $out/bin
            cp ${./mybinaryprogram} $out/bin/mybinaryprogram
            wrapProgram $out/bin/mybinaryprogram \
              --prefix LD_LIBRARY_PATH : "${libPath}"
          '';
      }

This can be built with:

` $ nix-build mybinaryprogram.nix`

And run with:

` $ ./result/bin/mybinaryprogram`

What are channels and how they get updated?
-------------------------------------------

[Nixpkgs](https://github.com/NixOS/nixpkgs) is the repository containing all packages and nixos modules/expressions.

A "channel" consists of a copy of the nixpkgs repository at a particular commit, and a url to the binary cache. The channel has no information about which binaries are available; it's just a copy of nixpkgs. When you install software, nix will ask the binary cache for that nix store path in order to download the actual binaries.

Available nixos.org channels are: `nixpkgs-unstable`, `nixos-unstable`, `nixos-<stable>` and `nixos-<stable>-small`, where `<stable>` refers to a stable version. You can see these channels at <https://nixos.org/channels/>, and find more detailed information at <http://nixos.org/nixos/manual/sec-upgrading.html>. Naturally, the `-<stable>` channels track the related stable branch, while `-unstable` channels track the master branch.

Channel update works as follows:

1.  Each channel has a particular job at `hydra.nixos.org` which must succeed:
    1.  For nixos: the trunk-combined ["tested"](http://hydra.nixos.org/job/nixos/trunk-combined/tested) job, which contains some automated nixos tests.
    2.  For nixos-small: the unstable-small ["tested"](http://hydra.nixos.org/job/nixos/unstable-small/tested) job.
    3.  For nixpkgs: the trunk ["unstable"](http://hydra.nixos.org/job/nixpkgs/trunk/unstable) job, which contains some critical release packages.

2.  Once the job succeeds at a particular nixpkgs commit, `cache.nixos.org` will download binaries from `hydra.nixos.org`.
3.  Once the above download completes, the channel updates.

You can also use nixos-unstable on non-nixos. Remember that the channel is just a copy of nixpkgs and nix will ask the binary cache for a particular hash.

You can also checkout the nixpkgs git and reset it to a particular commit of a channel. This will not affect your access to the binary cache.

How do I know where's nixpkgs channel located and at which commit?
------------------------------------------------------------------

First `echo $NIX_PATH` to see where nix looks for the expressions. Note that nix-env uses ~/.nix-defexpr regardless of $NIX_PATH.

If you want to know where <nixpkgs> is located: `nix-instantiate --find-file nixpkgs`

To know the commit, open the .version-suffix file in the nixpkgs location. The hash after the dot is the git commit.

An error occurs while fetching sources from an url, how do I fix it?
--------------------------------------------------------------------

First try to update the local nixpkgs expressions with `nix-channel --update` (these describe where to download sources from and how to build them). Try your build again and the url might have already been correctly updated for the package in question. You can also subscribe the unstable channel (which includes the most up-to-date expressions) with `nix-channel --add http://nixos.org/channels/nixpkgs-unstable`, update and try the build again.

If that fails you can update the url in the nix expression yourself. [Navigate to your channel's expressions](https://nixos.org/wiki/FAQ#How_do_I_know_where.27s_nixpkgs_channel_located_and_at_which_commit.3F) and find the package in one of the subdirectories. Edit the respective `default.nix` file by altering the `url` and `sha256`. You can use `nix-prefetch-url url` to get the SHA-256 hash of source distributions. If the shell complains that you do not have write privileges for the file system, you will have to enable them.

     # start a new shell with a private mount namespace (Linux-only)
     sudo unshare -m bash
     # remount the filesystem with write privileges (as root)
     mount -o remount,rw /nix/store
     # update the file
     nano <PATH_TO_PACKAGE>/default.nix
     # exit to shell where /nix/store is still mounted read-only
     exit

Be sure to [report the incorrect url](https://github.com/NixOS/nixpkgs/issues) or [fix it yourself](https://github.com/NixOS/nixpkgs/pulls).

How do I know the sha256 to use with fetchgit?
----------------------------------------------

The easiest trick is to put a fake sha256, like do `echo foo|sha256sum` in the shell and copy the resulting sha256. Now build your package, then Nix will fail to verify the downloaded git but will tell you the expected sha256.

Please note that you must not use the sha256 of any existing derivation.

Should I use <http://hydra.nixos.org/> as a binary cache?
---------------------------------------------------------

Short answer: no.

<http://cache.nixos.org> is hosted on AWS S3, so it is fast and efficient, but it only contains binaries for nix channels (nixos-unstable, nixpkgs-unstable, and some stable channels too). So, if the channel is way behind, you may see recommendations to add <http://hydra.nixos.org/> as a binary cache, as it contains all recent builds, including ones not pushed to channels.

Unfortunately, due to poor caching, <http://hydra.nixos.org/> needs to calculate what is available *every time* you ask about substitutes, so using it regularly as a cache will slow down the build machines.

Thus, the recommended configuration is to **not** put <http://hydra.nixos.org/> in any config file or automated script. But if you are working on nixpkgs master, then the binary cache provided by Hydra can be very helpful. In such a case, you can add the Hydra cache to the trusted binary caches in nix.conf or configuration.nix, restart the Nix daemon, and use `--option extra-binary-caches http://hydra.nixos.org/` in whatever commands are convenient.