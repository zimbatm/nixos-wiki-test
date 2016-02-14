---
title: Nix on OS X
permalink: /Nix_on_OS_X/
---

Status
======

**(as of Jul 19, 2015)**

| OS X Version     | Xcode/CL Tools for Xcode Version | Nix Installation                                                         | Status                       |
|------------------|----------------------------------|--------------------------------------------------------------------------|------------------------------|
| 10.6.x           | 3.2.6 - 4.2                      |                                                                          | unsupported                  |
| 10.7.x           | 4.6.3                            |                                                                          | unsupported                  |
| 10.8.x           | 5.1.1                            |                                                                          | unsupported                  |
| 10.9.x           | 5.x                              |                                                                          | unsupported                  |
| 10.9.x - 10.10.x | 6.x                              | <http://nixos.org/nix/download.html>                                     | supported                    |
| 10.8.x - 10.10.x | (none needed)                    | [The Pure Darwin stdenv](/Nix_on_OS_X#The_Pure_Darwin_stdenv "wikilink") | under development (unstable) |

### OS X 10.9 "Mavericks" & OS X 10.10 "Yosemite"

Nix is supported for OS X versions 10.9 and 10.10.

### Documentation

Users are encouraged to read the standard Nix documentation:

-   <http://nixos.org/nix/manual/>
-   <http://nixos.org/nixpkgs/manual/>

### Disclaimer

-   Some packages may be broken. See [reporting issues](/Nix_on_OS_X#Reporting_Issues "wikilink")
    -   Users who want to hack on [nixpkgs](https://github.com/NixOS/nixpkgs) to fix and modify packages can [follow the instructions](/Nix_on_OS_X#Using_a_clone_of_nixpkgs "wikilink") below.
-   Installation of some packages may take longer than usual because cached binaries may not exist for them yet.

### Requirements

-   Xcode 6.x or Command Line Tools for Xcode 6.x: <https://developer.apple.com/downloads>

### Installation

To install Nix, follow the instructions at <http://nixos.org/nix/download.html>

### Reporting Issues

-   Please report issues with broken packages at <https://github.com/NixOS/nixpkgs/issues>
-   IRC: `##nix-darwin` on `freenode.net`

Using a clone of nixpkgs
========================

### Instructions

1. [Install Nix](http://nixos.org/nix/download.html).

2. Clone the Nixpkgs git repository:

    $ git clone https://github.com/NixOS/nixpkgs.git

3. Configure Nix to use the git repository as its source for packages:

    $ cd ~/.nix-defexpr
    $ rm -rf channels
    $ ln -s /path/of/nixpkgs/clone nixpkgs

4. Put the following line in your `~/.bashrc` (or `~/.zshrc`, etc.):

    export NIX_PATH=nixpkgs=/path/of/nixpkgs/clone

5. Restart your shell to load these changes.

6. To download binaries, when available, instead of building packages, create the file /etc/nix/nix.conf:

    $ sudo mkdir /etc/nix
    $ sudo $EDITOR /etc/nix/nix.conf

-   For users on **10.9**, put the following line in that file:



     binary-caches = http://cache.nixos.org/ http://hydra.nixos.org/

-   For users on **10.10 and above**, put the following line in that file:



     binary-caches = http://zalora-public-nix-cache.s3-website-ap-southeast-1.amazonaws.com/ http://cache.nixos.org/ http://hydra.nixos.org/

<!-- -->


**Caveats:**

-   The Zalora binary cache is populated from a 10.10 machine. Binaries delivered to 10.9 machines from this cache are known to have problems. See [1](https://github.com/joelteon/nixpkgs/issues/18) for more information and instructions how to repair issues on 10.9 caused by getting binaries built on 10.10.
-   It may take several days for binaries to become available after the nixpkgs git repository is updated.

7. You can now use Nix:

    $ nix-env -iA nixpkgs.hello
    ...
    $ hello
    Hello, world!

### Updating

To update your package definitions to the latest revision:

    $ cd /path/to/nixpkgs
    $ git pull

Optionally, after updating, you can rebuild your environment with:

    $ nix-env -u --leq

**NOTES**:

-   If you experience any problems after updating your package definitions, you should try rebuilding your environment.
-   You should rebuild your environment when switching between the default stdenv and the Pure Darwin stdenv.

The Pure Darwin stdenv
======================

### Background

There is an optional [stdenv](http://nixos.org/nixpkgs/manual/#chap-stdenv), based on work in [copumpkin/pure-darwin](https://github.com/copumpkin/nixpkgs/tree/pure-darwin). The goal of the Pure Darwin project is to increase the purity of the Nix environment on OS X by only using a set of minimal external dependencies and enabling sandbox builds. To achieve this, the stdenv uses a patched clang which prevents it from searching in system directories for libraries.

### Disclaimer

-   This stdenv is under development. **Many packages will be broken.**
    -   Fixes for some packages may be available for cherry-picking from [copumpkin/pure-darwin](https://github.com/copumpkin/nixpkgs/tree/pure-darwin).
    -   There are still many packages that depend on System Frameworks in OS X that are no longer automatically picked up by clang, and need to have them added manually.
-   Users looking for a more stable experience should use a [standard installation](http://nixos.org/nix/download.html).

### Helping Out

Join the development effort:

-   IRC: `##nix-darwin` on `freenode.net`
-   Please report issues with broken packages at <https://github.com/NixOS/nixpkgs/issues>
    -   **NOTE**: Make sure to specify that you are using the Pure Darwin stdenv.

### Resources

-   [Issue tracker for copumpkin/nixpkgs](https://github.com/copumpkin/nixpkgs/issues).
    -   **NOTE**: This issue tracker is no longer current, but remains a useful resource.

### Installation

**NOTES**:

-   If you have previously [installed Nix](http://nixos.org/nix/download.html), you can skip steps 1 & 2.
-   If you are already working from a [clone of nixpkgs](/Nix_on_OS_X#Using_a_clone_of_nixpkgs "wikilink"), see steps 3 & 7

1. Run the following shell command (as a user other than root):

    $ curl https://nixos.org/nix/install | sh

2. Restart your shell or enter this command:

    $ . $HOME/.nix-profile/etc/profile.d/nix.sh

3. Clone the Nixpkgs git repository and checkout the staging branch:

    $ git clone https://github.com/NixOS/nixpkgs.git
    $ cd nixpkgs
    $ git checkout staging

4. Configure Nix to use the git repository as its source for packages:

    $ nix-channel --remove nixpkgs
    $ cd ~/.nix-defexpr
    $ rm -rf *
    $ ln -s /path/of/nixpkgs/clone nixpkgs

5. Put the following line in your `~/.bashrc` (or `~/.zshrc`, etc.):

    export NIX_PATH=nixpkgs=/path/of/nixpkgs/clone

6. Restart your shell to load these changes.

7. Create a config file:

    mkdir ~/.nixpkgs
    $EDITOR ~/.nixpkgs/config.nix

with the contents:

    {
      replaceStdenv = { pkgs }: pkgs.allStdenvs.stdenvDarwinPure;
    }

8. You are now ready to use Nix:

    $ nix-env -iA nixpkgs.hello
    ...
    $ hello
    Hello, world!

### Updating

Instructions for updating your package definitions with the Pure Darwin stdenv are [the same as above](/Nix_on_OS_X#Updating "wikilink").

### Darwin Sandboxes

To use sandbox support (the Darwin equivalent of a Unix chroot) with the Pure Darwin stdenv:

    $ nix-env -i <package> --option build-use-chroot true --option use-binary-caches false

To globally enable sandboxes, add


    build-use-chroot = true

to `/etc/nix/nix.conf`

**NOTES:**

-   See [below](/Nix_on_OS_X#Is_there_anything_else_I_should_look_out_for.3F "wikilink") for more information about sandboxes.

### Frequently Asked Questions

#### What is a stdenv?

See the [documentation](/Nix_on_OS_X#Documentation "wikilink"), specifically, [this part](http://nixos.org/nixpkgs/manual/#chap-stdenv).

#### How do I use the Pure Darwin stdenv?

See the [instructions](/Nix_on_OS_X#Installation_3 "wikilink").

**TL; DR:**

1. Use the staging branch.

2. Add

    replaceStdenv = { pkgs }: pkgs.allStdenvs.stdenvDarwinPure;

to `~/.nixpkgs/config.nix`

#### Do I need Xcode or the Xcode Command Line Tools for to use the Pure Darwin stdenv?

No, the Pure Darwin stdenv doesn't require Xcode to be present on your computer; the only dependency is `/usr/bin/curl`.

However, the Xcode developer SDKs contain Frameworks that some packages still need to build, such as Vim with system clipboard support. Until we have a consistent solution for all of the Apple SDK stuff, it’s a good idea to keep Xcode installed.

#### How do packages pull in Apple-specific frameworks if clang can’t use the system ones anymore?

For this, we have `pkgs.darwin`, an attribute list of Darwin-specific packages. If your build needs a framework, look for `darwin.apple_sdk.frameworks.${frameworkName}`

#### Is there anything else I should look out for?

Unfortunately, the sandbox is more restrictive than we’d really like. It doesn’t allow creation of Unix sockets in general, and you can only whitelist Unix sockets by giving the sandbox an absolute path to the socket you want. It also currently prevents all network access. The strictness of the sandbox will cause some test suites to incorrectly fail, such as Nix’s and packages that use networking functions in their tests. If you see messages like “operation not permitted” or “cannot bind to socket” in your build output, try turning off the sandbox for that build.

If you don’t want to have to disable the sandbox just to build packages, we recommend turning it off globally and only enabling it if you’re doing dev work on [nixpkgs](http://github.com/NixOS/nixpkgs).

#### Is there a good way to debug a package that only fails in the sandbox?

If you’re having trouble, you should add

    darwin-log-sandbox-violations = true

to `/etc/nix/nix.conf` . Anytime a build tries to access a file it’s not allowed to, the file path and culprit will be logged to the system log, which you can view with Console.app. The filter `Sandbox:` will show you sandbox violations.