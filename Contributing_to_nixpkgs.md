---
title: Contributing to nixpkgs
permalink: /Contributing_to_nixpkgs/
---

Overview
--------

This is the easiest and more straightforward way I know to modify and expand [nixpkgs](https://nixos.org/nixpkgs/). I have annoyed many people, specially [Viric](/User:Viric "wikilink"), to get to this point. I hope this article will help future newbies so the experts can focus on other stuff.

This document is not intended as a formal approach to learn [nix](https://nixos.org/nix/), [nixpkgs](https://nixos.org/nixpkgs/), [NixOS](https://nixos.org/nixos/) or *systems administration*. It is just a sort of tutorial for newbies to be able to easily fix and contribute trivial improvements. I believe this approach will help newbies such as myself to get (slowly and inefficiently) educated on the *nix language*, the *nix package manager* and *NixOS*.

The official nixpkgs sources are managed on a [git](http://git-scm.com/) repository at [GitHub](https://github.com). And GitHub implements most of the tools for the nixpkgs development workflow.

*All operations explained on this document, if tested, have been tested on a [NixOS](https://nixos.org/nixos/) system.*

Cloning the "NixOS/nixpkgs" repository on GitHub
------------------------------------------------

-   I had to sign up at GitHub. This is free (as in beer) as of 2013-03-20.
-   Then I visited [the official nixpkgs repository](https://github.com/NixOS/nixpkgs) and hit the *fork* button.
-   This results in your own somewhat-independent repository: `https://github.com/your_username/nixpkgs`

Retrieving a working copy of your repository
--------------------------------------------

-   Now you can clone your repository somewhere on your system:
        mkdir ~/devel/ && cd ~/devel/ && git clone https://github.com/your_username/nixpkgs.git

-   You should get a git repository with the whole nixpkgs tree under `~/devel/nixpkgs/`
-   Unless explicitly stated, for the rest of the document we are working from this directory:
        cd ~/devel/nixpkgs/

-   Just to ensure everything is clear, on this directory there should be two main entries: the `default.nix` file and the `pkgs/` directory.
-   From now on, `pkgs/` will refer to this `nixpkgs/pkgs/` so if you keep the equivalent of `~/devel/nixpkgs/` as your CWD (current working directory), most commands and pathnames can be copied and pasted in real life.
-   So for example, on this document `pkgs/top-level/` refers to `~/devel/nixpkgs/pkgs/top-level/`
-   This looks perhaps too verbose, but I feel this extra context would have clarified many ambiguous explanations to me.
-   Also, when talking about directories I will try to always include the trailing slash.

Following the white rabbit
--------------------------

-   I will center this tutorial on the nix expression that packages the [i3 window manager](http://i3wm.org/), as this expression is quite simple and it was contributing to this expression that I got to understand this whole workflow I am documenting.
-   In `~/devel/nixpkgs/default.nix` there is an *import* statement that reads `import ./pkgs/top-level/all-packages.nix`
-   I have the feeling that most references between nix sources (*nix expressions*) in *nixpkgs* are stated as relative paths pointing up and down the `nixpkgs/` directory hierarchy.
-   Please have a look at this `pkgs/top-level/all-packages.nix`. There are many more or less complex definitions at the beginning, and then a shitload of declarations such as:

<!-- -->

    i3       = callPackage ../applications/window-managers/i3 { };
    i3lock   = callPackage ../applications/window-managers/i3/lock.nix { };
    i3status = callPackage ../applications/window-managers/i3/status.nix { };

and

    pango = callPackage ../development/libraries/pango/1.30.x.nix { };

-   Now let's have a look at the contents of `pkgs/applications/window-managers/i3/`:

<!-- -->

    $ ls -1 pkgs/applications/window-managers/i3/
    default.nix
    lock.nix
    status.nix

-   The first assignment `i3 = callPackage ...` refers implicitly to `default.nix` on the `pkgs/applications/window-managers/i3/` directory.
-   The second and third assignments are explicitly referring to `lock.nix` and `status.nix` files on this `pkgs/applications/window-managers/i3/` directory.
-   Each of these three files contain somewhat equivalent *nix expressions* that the *nix package manager* will consider as *packages* thanks to the `callPackage` on `pkgs/top-level/all-packages.nix`.

The actual package "recipe"
---------------------------

-   We will dive (not too deep, though) on `pkgs/applications/window-managers/i3/default.nix` which is the nix expression that corresponds to the package for the i3 window manager:

<!-- -->

    { fetchurl, stdenv, which, pkgconfig, libxcb, xcbutilkeysyms, xcbutil,
      xcbutilwm, libstartup_notification, libX11, pcre, libev, yajl,
      libXcursor, coreutils, perl, pango }:

    stdenv.mkDerivation rec {
      name = "i3-${version}";
      version = "4.5.1";

      src = fetchurl {
        url = "http://i3wm.org/downloads/${name}.tar.bz2";
        sha256 = "bae55f1c7c4a21d71aae182e4fab6038ba65ba4be5d1ceff9e269f4f74b823f2";
      };

      buildInputs = [ which pkgconfig libxcb xcbutilkeysyms xcbutil xcbutilwm
        libstartup_notification libX11 pcre libev yajl libXcursor perl pango ];

      patchPhase = ''
        patchShebangs
      '';

      configurePhase = "makeFlags=PREFIX=$out";

      meta = {
        description = "i3 is a tiling window manager";
        homepage = "http://i3wm.org";
        maintainers = [ stdenv.lib.maintainers.garbas ];
        license = stdenv.lib.licenses.bsd3;
      };
    }

-   As an example of the trivial fixes and improvements I was talking about, you can have a look at two changesets (*commits* in the *git* terminology) I did:
    -   adding the *pango* dependency to [enable antialiased fonts on i3](https://github.com/NixOS/nixpkgs/commit/f976962d7b7087920a6635ca90f07b06e8a44202)
    -   and [updating from upstream version 4.5 to 4.5.1](https://github.com/NixOS/nixpkgs/commit/ae9b33ac51dee8b24479cbdb54fe8d752a912ce4)
-   As I anticipated above, I am not going to explain what's the purpose of the list of `buildInputs`, how a list enclosed by square brackets separated by spaces differs from the list at the beginning or the relevance of the colon that follows its closing curly brace. I just don't know so much *yet*.

Making changes
--------------

-   On your local clone of your forked repository you can edit whatever you want.
-   That is, in our example, under `~/devel/nixpkgs/`
-   As you can see on the *commits* cited above I edited `pkgs/applications/window-managers/i3/default.nix`. I did several changes at once:
    -   Added `pango` to both dependency lists. This `pango` refers to `pango = callPackage ...` in `pkgs/top-level/all-packages.nix`
    -   Changed the `version` string.
    -   Changed the checksum for the tarball on the `sha256` string.
    -   Removed a line on the `patchPhase` string. This line was a `sed` command which was erasing some lines from the file `common.mk`, which is part of the sources in the tarball, before building the application.
-   And in an attempt to extract something positive from the issue, we are going to review an additional [unfortunate change](https://github.com/NixOS/nixpkgs/commit/1eb437784021521d57ae96980bf447e9162d4415) I made on `pkgs/top-level/all-packages.nix`
-   To figure out the checksum for the tarball you have the `nix-prefetch-url` utility. In addition to returning the *sha256 checksum* it will also leave the downloaded blob on your *nix store*, thus avoiding additional downloads when you test the build process. You get the path to the downloaded blob and the *sha256sum* on the last two lines of the command's output:

<!-- -->

    $ nix-prefetch-url 'http://i3wm.org/downloads/i3-4.5.1.tar.bz2'
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100  858k  100  858k    0     0   866k      0 --:--:-- --:--:-- --:--:--  867k
    path is "/nix/store/clvs3jvyk24bskkn5pfd7fjpyaa1svsx-i3-4.5.1.tar.bz2"
    1wi3p1s4z7r6kvzwxlg59fx6bfiqc2mlybhqmqddf8aaghf5zrds

-   Almost out of scope here, but you can easily review your changes. Go get some *git* documentation if you need:

<!-- -->

    $ git diff
    diff --git a/pkgs/applications/window-managers/i3/default.nix b/pkgs/applications/window-managers/i3/default.nix
    index f8e4e16..4173660 100644
    --- a/pkgs/applications/window-managers/i3/default.nix
    +++ b/pkgs/applications/window-managers/i3/default.nix
    @@ -1,21 +1,20 @@
     { fetchurl, stdenv, which, pkgconfig, libxcb, xcbutilkeysyms, xcbutil,
       xcbutilwm, libstartup_notification, libX11, pcre, libev, yajl,
    -  libXcursor, coreutils, perl }:
    +  libXcursor, coreutils, perl, pango }:

     stdenv.mkDerivation rec {
       name = "i3-${version}";
    -  version = "4.5";
    +  version = "4.5.1";

       src = fetchurl {
         url = "http://i3wm.org/downloads/${name}.tar.bz2";
    -    sha256 = "1kiffcbvvjljqchw9ffgy9s8f9z06i8805jvjas58q5i2yxl5kcy";
    +    sha256 = "bae55f1c7c4a21d71aae182e4fab6038ba65ba4be5d1ceff9e269f4f74b823f2";
       };

       buildInputs = [ which pkgconfig libxcb xcbutilkeysyms xcbutil xcbutilwm
    -    libstartup_notification libX11 pcre libev yajl libXcursor perl ];
    +    libstartup_notification libX11 pcre libev yajl libXcursor perl pango ];

       patchPhase = ''
    -    sed -i -e '/^# Pango/,/^$/d' common.mk
         patchShebangs .
       '';
    diff --git a/pkgs/top-level/all-packages.nix b/pkgs/top-level/all-packages.nix
    index 0d3e4cc..a1c0d4b 100644
    --- a/pkgs/top-level/all-packages.nix
    +++ b/pkgs/top-level/all-packages.nix
    @@ -7224,7 +7224,9 @@ let

       hydrogen = callPackage ../applications/audio/hydrogen { };

    -  i3 = callPackage ../applications/window-managers/i3 { };
    +  i3 = callPackage ../applications/window-managers/i3 {
    +    cairo = cairo.override { xcbSupport = true; };
    +  };

       i3lock = callPackage ../applications/window-managers/i3/lock.nix {
         cairo = cairo.override { xcbSupport = true; };

Testing the changes
-------------------

-   The changes made to these *nix expressions* are affecting the build process and therefore the behavior of the resulting binaries, configuration and other support files (man pages, scripts, etc.). Additionally there might be other packages or system services that depend on the ones we are altering, so these changes should be carefully considered, reviewed and thoroughly tested before they can be submitted and accepted on the official nixpkgs repository.
-   The worst case scenario is that a lot of people will get a broken package (or a broken system, even unable to boot) if the changes have unexpected side effects or if the changes are wrong and are reviewed too lightly. In special this happens more often for architectures other than 32 and 64-bit x86, because these are not so popular and thus less known and tested.
-   If your current directory is, as we said, `~/devel/nixpkgs/`, you can request nix to build the modified package (see `man nix-build`):

<!-- -->

    nix-build -A i3

-   Alternatively, you can request nix to build from expressions on an specific directory (`-f .`)and then to update your user environment so you will have the newly built binaries, man pages etc. available at hand for testing (see `man nix-env`):

<!-- -->

    nix-env -f . -iA i3

-   In these two invocations the `i3` in `-A i3` refers to `i3 = callPackage ...` in `pkgs/top-level/all-packages.nix`. In the same way as the `pango` we talked about above, both names are attributes from the top-level Nix expression being evaluated. In this case `~/devel/nixpkgs/default.nix`
-   Here I would have detected the error I introduced on `pkgs/top-level/all-packages.nix`. Too bad I didn't still know how to test my changes:

<!-- -->

    $ nix-build -A i3
    error: function at `~/devel/nixpkgs/pkgs/applications/window-managers/i3/default.nix:1:1' called with unexpected argument
    (use `--show-trace' to show detailed location information)

-   Let's follow the advice by adding `--show-trace` to the `nix-build` invocation:

<!-- -->

    $ nix-build --show-trace -A i3
    error: while evaluating the function at `~/devel/nixpkgs/pkgs/lib/customisation.nix:98:35':
    while evaluating the function at `~/devel/nixpkgs/pkgs/lib/customisation.nix:59:24':
    while evaluating the builtin function `isAttrs':
    function at `~/devel/nixpkgs/pkgs/applications/window-managers/i3/default.nix:1:1' called with unexpected argument

-   Not that I got any more clues from this extra step. But it turned out that there is no mention to `cairo` on `pkgs/applications/window-managers/i3/default.nix` so it was illegal to invoke the `cairo.override ...` thing inside the `i3 = callPackage ...` on `pkgs/top-level/all-packages.nix`:

<!-- -->

    i3 = callPackage ../applications/window-managers/i3 {
        cairo = cairo.override { xcbSupport = true; }; # WRONG!
      };

-   So I would have reverted my changes to `pkgs/top-level/all-packages.nix`. Take care with this command as it is quite eager to undo your changes. But that's out of scope here. See some *git* documentation:

<!-- -->

    git checkout pkgs/top-level/all-packages.nix

-   Now let's try again:

<!-- -->

    $ nix-build -A i3
    /nix/store/agld76p9rgvn3j610z8kgppz67f8kmg7-i3-4.5.1

-   When the build succeeds, `nix-build` will leave the result linked as `./result` on the current working directory:

<!-- -->

    $ stat ./result | head -n 1
      File: "./result" -> "/nix/store/agld76p9rgvn3j610z8kgppz67f8kmg7-i3-4.5.1"

-   If convenient, you can review and fiddle with the resulting stuff:

<!-- -->

    $ find result/ -type f
    result/include/i3/ipc.h
    result/bin/i3-input
    result/bin/i3-nagbar
    result/bin/i3-msg
    result/bin/i3-migrate-config-to-v4
    result/bin/i3-sensible-terminal
    result/bin/i3-sensible-pager
    result/bin/i3-sensible-editor
    result/bin/i3bar
    result/bin/i3
    result/bin/i3-config-wizard
    result/bin/i3-dmenu-desktop
    result/bin/i3-dump-log
    result/etc/i3/config.keycodes
    result/etc/i3/config
    result/share/xsessions/i3.desktop
    result/share/applications/i3.desktop

-   And for some real-life testing we would invoke `nix-env` as explained above:

<!-- -->

    $ nix-env -f . -iA i3
    replacing old `i3-4.5.1'
    installing `i3-4.5.1'
    building path(s) `/nix/store/49ag28h77sjmg3rygpsm83jxkhn5jzyn-user-environment'
    created 2006 symlinks in user environment

-   You might double-check that the binaries on `$PATH` are indeed the new ones:

<!-- -->

     stat `type -P i3` | head -n 1
      File: "~/.nix-profile/bin/i3" -> "/nix/store/agld76p9rgvn3j610z8kgppz67f8kmg7-i3-4.5.1/bin/i3"

-   It is quite annoying to test a window manager, `i3` in special as [it does not implement the `--replace` feature](http://bugs.i3wm.org/report/ticket/536).
-   And sometimes there are lots of other tricky possibilities. For example `i3` invokes other binaries from the same package, such as `i3bar`. I lost a great deal of time trying to figure out why the *i3 status bar* was refusing to render *pango fonts*. That was because I was launching `~/devel/nixpkgs/result/bin/i3` instead of having the whole package replaced on my *nix user environment*. And the old `i3bar` from the *nix store* was on my `$PATH`. Obviously that was before I learned the `nix-env` invocation shown above.

Pushing the local changes to GitHub
-----------------------------------

Sending a pull request to the official nixpkgs repository
---------------------------------------------------------

Waiting for your changes to appear on the *nix channel*
-------------------------------------------------------

Beyond this point (and getting help)
------------------------------------

-   See <https://nixos.org/development/>
