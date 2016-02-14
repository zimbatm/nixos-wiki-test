---
title: How to install nix in home (on another distribution)
permalink: /How_to_install_nix_in_home_(on_another_distribution)/
---

Introduction
------------

Our goal is to install Nix into our home directory as an unprivileged user. Possible use cases include:

-   You don't have root access, but don't want to present your sysadmin with a long list of dependencies to install.
-   You want to convince a skeptical sysadmin to use Nix.
-   You need the same development environment across several machines.
-   You need to quickly deploy software in several locations
-   You're already used to Nix, or you're just learning.

PRoot Installation
------------------

<i>(the easy way)</i>

On Linux [PRoot](http://proot.me/) is a user-space implementation of chroot, mount --bind, and binfmt_misc. (Note that PRoot intercepts system calls and is therefore slower than a native installation, but it is great for painless installation on environments where you have no root. Future versions of PRoot may be faster).

First install PRoot, a binary is provided, e.g.,

` mkdir ~/opt/bin`
` cd ~/opt/bin`
` wget `[`http://static.proot.me/proot-x86_64`](http://static.proot.me/proot-x86_64)
` chmod u+x proot-x86_64`

Fetch a Nix binary install, e.g.,

` mkdir ~/opt/nix-mnt`
` cd ~/opt/nix-mnt`
` wget `[`http://hydra.nixos.org/build/10272842/download/1/nix-1.7-x86_64-linux.tar.bz2`](http://hydra.nixos.org/build/10272842/download/1/nix-1.7-x86_64-linux.tar.bz2)
` tar xvjf nix-*bz2`

Now mount it as a /nix directory using PRoot (some magic here!)

` ~/opt/bin/proot-x86_64 -b ~/opt/nix-mnt/nix-1.7-i686-linux/:/nix`
` bash`
` ls /nix`

Run the Nix install

` cd /nix`
` ./install`

And, presto!

` . ~/.nix-profile/etc/profile.d/nix.sh`
` nix-env -qa '*' |wc -l`
` 13343 `

13K+ packages at your finger tips! Install Ruby

` nix-env -qa ruby`
`   ruby-1.8.7-p374`
`   ruby-1.9.3-p547`
`   ruby-2.0.0-p481`
`   ruby-2.1.0-p0`
`   ruby-2.1.1-p0`
`   ruby-2.1.2-p353`

` nix-env -i ruby-2.1.0-p0`

Gives a \*binary\* install of that ruby

` ruby -v`
`   ruby 2.1.0 (2014-02-06) [i686-linux]`

Manual Installation
-------------------

<i>(the hard way)</i>

The primary trade-off with a home directory installation is that you will be unable to download pre-build binaries from Hydra because hash calculation takes the absolute path of the Nix store into account.

Our ultimate goal is to have a Nix store at `$HOME/nix/store` and a default user profile linked to `$HOME/.nix-profile`, as under NixOS. We will want to have Nix installed in that profile by Nix itself so that future updates are automatic, but we must bootstrap by building Nix manually. (We cannot use one of the pre-built binaries available on the Nix website because the Nix store path is hard-coded to `/nix/store`.)

Bootstrapping
-------------

First, gather the dependencies. We do not have root access, so we will download and build most of the dependencies ourselves. We assume that the host system provides GCC, make, pkgconfig, and Perl.

We will unpack, build, and install everything in `$HOME/nix-boot` so that we can easily remove it all when we're done bootstrapping.

` mkdir ~/nix-boot`
` cd ~/nix-boot`

Download our dependencies into this directory:

-   [Nix](http://nixos.org/releases/nix/nix-1.6.1/nix-1.6.1.tar.xz)
-   [bzip2](http://bzip.org/1.0.6/bzip2-1.0.6.tar.gz)
-   [cURL](http://curl.haxx.se/download/curl-7.35.0.tar.lzma)
-   [SQLite3](http://www.sqlite.org/2014/sqlite-autoconf-3080300.tar.gz)
-   [DBI](http://search.cpan.org/CPAN/authors/id/T/TI/TIMB/DBI-1.631.tar.gz)
-   [DBD::SQLite](http://search.cpan.org/CPAN/authors/id/I/IS/ISHIGAKI/DBD-SQLite-1.40.tar.gz)
-   [WWW::Curl](http://search.cpan.org/CPAN/authors/id/S/SZ/SZBALINT/WWW-Curl-4.15.tar.gz)

Download and unpack the source tarballs above into `$HOME/nix-boot`.

### Environment

We will set some environment variables so our build tools can find the bootstrapping tools:

` export PATH=$HOME/nix-boot/bin:$PATH`
` export PKG_CONFIG_PATH=$HOME/nix-boot/lib/pkgconfig:$PKG_CONFIG_PATH`
` export LDFLAGS="-L$HOME/nix-boot/lib $LDFLAGS"`
` export CPPFLAGS="-I$HOME/nix-boot/include $CPPFLAGS"`
` export PERL5OPT="-I$HOME/nix-boot/lib/perl"`

------------------------------------------------------------------------

Note: on some Linux systems, you may need to change `PERL5OPT`, for example to:

` export PERL5OPT="-I$HOME/nix-boot/lib64/perl5"`

### bzip2

To install bzip2, run the following commands from the bzip2 source directory:

` make -f Makefile-libbz2_so`
` make install PREFIX=$HOME/nix-boot`
` cp libbz2.so.1.0 libbz2.so.1.0.6 $HOME/nix-boot/lib`

### cURL and SQLite3

We build cURL and SQLite3 with the same sequence of commands in each of their respective source directories:

` ./configure --prefix=$HOME/nix-boot`
` make`
` make install`

### Perl modules

Install the Perl modules DBI, DBD::SQLite, and WWW::Curl in that order with the commands

` perl Makefile.PL PREFIX=$HOME/nix-boot`
` make`
` make install`

### Nix

At last, we are ready to install Nix. At first, we will install Nix into the `$HOME/nix-boot` prefix so we can remove it later. From the Nix source directory,

` ./configure --prefix=$HOME/nix-boot --with-store-dir=$HOME/nix/store --localstatedir=$HOME/nix/var`
` make`
` make install`

Installing Nix
--------------

Now we want to install Nix to the default user profile. First, we need a copy of Nixpkgs. If you are planning to do development work on Nixpkgs, you may just want to `git clone` a copy of the repository and symlink `$HOME/.nix-defexpr` to your copy. Otherwise, we will use `nix-channel` to download the Nixpkgs channel:

` nix-channel --add `[`http://nixos.org/channels/nixpkgs-unstable`](http://nixos.org/channels/nixpkgs-unstable)
` nix-channel --update`

Next, we will configure Nixpkgs. The default options for Nix will cause it to use the store at `/nix/store`, so we need to override the Nix expression options. Create the file `$HOME/.nixpkgs/config.nix` with the following contents:

` pkgs:`
` {`
`   packageOverrides = self: {`
`     nix = self.nix.override {`
`       # Replace $HOME with your actual home directory; it will not`
`       # be expanded for you.`
`       # Also, the double quotes around the path are mandatory.`
`       storeDir = "$HOME/nix/store";`
`       stateDir = "$HOME/nix/var";`
`     };`
`   };`
` }`

Now, just install Nix:

` nix-env -iA nix`

Finalizing
----------

Finally, we need to add the default user profile to our environment. Create a link from `$HOME/.nix-profile` to the default user profile, like on NixOS:

` ln -s $HOME/nix/var/nix/profiles/default $HOME/.nix-profile`

We want to add the default user profile to our `PATH` whenever we log in, so add the following line to your `.profile`, `.bashrc`, or wherever you usually put environment variable settings:

` export PATH=$HOME/.nix-profile/bin:$PATH`

At this point, you probably want to log out and log back in to make sure the environment settings take effect. Check that `nix-env` is on your path:

` which nix-env`

Assuming this command was successful and doesn't point to the bootstrapped version of Nix in `$HOME/nix-boot`, we can safely delete `$HOME/nix-boot` entirely!

You're done! You should now be able to use Nix normally. Assuming you keep your Nixpkgs channel up to date, Nix will handle its own updates in the future.

References
----------

[Setting up a multi-user Nix installation on non-NixOS systems - Sander van der Burg](http://sandervanderburg.blogspot.com/2013/06/setting-up-multi-user-nix-installation.html)

[running the nix package manager in a prefix as the home directory](http://blog.lastlog.de/posts/running_the_nix_package_manager_in_a_prefix_as_the_home_directory/)