---
title: Create and debug nix packages
permalink: /Create_and_debug_nix_packages/
---

This article describes how to work with the nix related repositories to add new packages, edit and debug existing packages. For details on the NixOS module system see [NixOS:Modules](/NixOS:Modules "wikilink"). [NixOS:extend_NixOS](/NixOS:extend_NixOS "wikilink") explains how to write, test and debug your own modules.

The nix repositories are hosted here: <https://github.com/nixos>

Basics
------

The code for nix packages is managed in the nixpkgs repository. NixOS services, and other system configuration options are managed in the nixos sub-directory of the nixpkgs repository.

The steps to take for your first change should look something like this:

1.  Fork the repo (e.g. click the fork button on <https://github.com/nixos/nixpkgs>).
2.  Clone your fork `git clone git@github.com:YOUR-GITHUB-ACCOUNT-NAME/nixpkgs.git`
3.  Hack hack hack
4.  Push your changes to your fork
5.  Open a pull request
6.  Profit!

How to install from the local repository
----------------------------------------

For the sake of this article, let's set an environment variable which points to the directory where we've cloned our repo.

` $ export NIXPKGS=/path/to/clone/of/nixpkgs`

make some changes ...

**example: list all available software** from the local repository $NIXPKGS/nixpkgs

` $ nix-env -f $NIXPKGS -qaP '*'`

**example: install software from local repository**

` $ nix-env -f $NIXPKGS -i python-urlgrabber`

**example: update the system** based on your local **$NIXPKGS**

` $ nixos-rebuild -I $NIXPKGS switch`

**example: build an expression and put the output in to \`pwd\`/results**

` $ nix-build $NIXPKGS -A irssi`

**example: get an environment which is used to build irssi (also see nix-shell)**

` $ nix-build $NIXPKGS --run-env -A irssi`

**example: get a persistent environment which is used to build irssi**

` $ nix-build $NIXPKGS --run-env -A irssi --add-root`

Tracking upstream changes and avoiding extra rebuilding
-------------------------------------------------------

You have forked the relevant nix repository, but you will want to track changes in the upstream nix repo too. You can add a remote, and a corresponding branch for this.

` git remote add upstream `[`https://github.com/NixOS/nixpkgs.git`](https://github.com/NixOS/nixpkgs.git)

You can create a branch to track the upstream master branch:

` $ git checkout -b upstream-master upstream/master`
` $ git pull`

This will put you into a branch with all the latest changes.

### NixOS

If you are on NixOS you can use \`nixos-version\` to see the relevant short revision hash:

` $ nixos-version `
` 14.11pre52727.5d97886 (Caterpillar)`
` ${NixOS release}.${nixpkgs revision}`

This string shows the **NixOS release number** (**14.11pre52727**) followed by the (**nixpkgs git repo revision**) used to produce your current system (**5d97886**).

### Nix (not on NixOS)

if you are not using NixOS but want to use hydra builds anyway simply list your channels:

` $ nix-channel --list`
` nixpkgs `[`https://nixos.org/channels/nixpkgs-unstable`](https://nixos.org/channels/nixpkgs-unstable)

then type this channel: **<https://nixos.org/channels/nixpkgs-unstable>** into your browser and obtain this redirect **<https://nixos.org/releases/nixpkgs/nixpkgs-15.05pre61664.bc4347b/>**. you can now use this **bc4347b** revision, or if you want to have an older revision, just go to: **<https://nixos.org/releases/nixpkgs/>**.

### how to checkout

` $ git branch`
` upstream-master`
` $ git pull`
` $ git checkout -b my-new-pkg 5d97886`
` Switched to a new branch 'my-new-pkg'`

After making some changes you can commit them into your local repo:

` $ git add foo`
` $ git commit`

Then you push your changes to your fork:

` $ git push origin my-new-pkg`

You can use this to open a pull request on github.

If some time has passed since you have created your fork, you will want to merge your changes with upstream and test that it still works.

` $ git fetch upstream`
` $ git merge upstream`

If your merge then fails because someone else has made the same change (for example, someone else also packaged a library you have just packed for the program you want to get into nixpkgs), then you can do this:

` $ git checkout my-new-pkg`
` $ git rebase master`

Next you have to test if your program works with the library packaged from someone else, then do:

` git checkout master`
` git log --stat`

and pick the commit where the library was added. Finally cherry-pick that commit into your branch:

` git checkout my-new-pkg`
` git cherry-pick 5d97886a6a545fb20495e0837cc50fa63d2a80e1`

Afterwards do your usual tests and if needed also make modifications to the library but keep in mind that this might break the other use-case of that library and if in doubt check that as well.

Using nix-shell for package development
---------------------------------------

nix-shell is a command which drops you into the build environment for a package. This is convenient for writing and debugging nix expressions. Nix-shell requires nix-1.6.x although running nix-build --run-env produces a similar environment.

`  mkdir -p /tmp/nix-shell-bc`
`  cd /tmp/nix-shell-bc`
`  nix-shell $NIXPKGS/nixpkgs  -A bc`
`  export out=/tmp/foo/out`

now we have find out which phases are specified for this package:

`   typeset -f genericBuild | grep 'phases='`
`   phases="$prePhases unpackPhase patchPhase $preConfigurePhases configurePhase $preBuildPhases buildPhase checkPhase $preInstallPhases installPhase fixupPhase installCheckPhase $preDistPhases distPhase $postPhases";`

The phases can be defined either as a string to be eval'ed or as a shell function, [this is how](https://github.com/NixOS/nixpkgs/blob/5a0b79/pkgs/stdenv/generic/setup.sh#L818) Nix invokes it.

so when developing you need to run these phases in a row:

`  unpackPhase`
`  patchPhase`
`  configurePhase`
`  buildPhase`
`  checkPhase`
`  installPhase`
`  fixPhase`
`  installCheckPhase`
`  installPhase`
`  distPhase`

To run a phase use **eval "${!phase:-phase}"**.

list all functions which are declared in **set**:

`  typeset -F`
`  declare -f addCVars`
`  declare -f addToCrossEnv`
`  declare -f addToNativeEnv`
`  declare -f addToSearchPath`
`  declare -f addToSearchPathWithCustomDelimiter`
`  declare -f buildPhase`
`  declare -f checkPhase`
`  declare -f closeNest`
`  declare -f command_not_found_handle`
`  declare -f configurePhase`
`  declare -f distPhase`
`  declare -f dumpVars`
`  declare -f ensureDir`
`  declare -f exitHandler`
`  declare -f findInputs`
`  declare -f fixLibtool`
`  declare -f fixupPhase`
`  declare -f genericBuild`
`  declare -f header`
`  declare -f installBin`
`  declare -f installCheckPhase`
`  declare -f installPhase`
`  declare -f patchELF`
`  declare -f patchPhase`
`  declare -f patchShebangs`
`  declare -f runHook`
`  declare -f showPhaseHeader`
`  declare -f startNest`
`  declare -f stopNest`
`  declare -f stripDirs`
`  declare -f stripHash`
`  declare -f substitute`
`  declare -f substituteAll`
`  declare -f substituteAllInPlace`
`  declare -f substituteInPlace`
`  declare -f unpackFile`
`  declare -f unpackPhase`

If the phase has been defined as a function, to list a particular function type:

`  typeset -f unpackPhase`

Otherwise, if it was a string, simply echo the variable related to it

`  echo $unpackPhase`

In either case, you can see the code that is about to be executed for each phase:

`  typeset -f unpackPhase`
`  unpackPhase () `
`  { `
`      runHook preUnpack;`
`      if [ -z "$srcs" ]; then`
`          if [ -z "$src" ]; then`
`              echo 'variable $src or $srcs should point to the source';`
`              exit 1;`
`          fi;`
`          srcs="$src";`
`      fi;`
`      local dirsBefore="";`
`      for i in *;`
`      do`
`          if [ -d "$i" ]; then`
`              dirsBefore="$dirsBefore $i ";`
`          fi;`
`      done;`
`      for i in $srcs;`
`      do`
`          unpackFile $i;`
`      done;`
`      if [ -n "$setSourceRoot" ]; then`
`          runHook setSourceRoot;`
`      else`
`          if [ -z "$sourceRoot" ]; then`
`              sourceRoot=;`
`              for i in *;`
`              do`
`                  if [ -d "$i" ]; then`
`                      case $dirsBefore in `
`                          *\ $i\ *)`
`  `
`                          ;;`
`                          *)`
`                              if [ -n "$sourceRoot" ]; then`
`                                  echo "unpacker produced multiple directories";`
`                                  exit 1;`
`                              fi;`
`                              sourceRoot="$i"`
`                          ;;`
`                      esac;`
`                  fi;`
`              done;`
`          fi;`
`      fi;`
`      if [ -z "$sourceRoot" ]; then`
`          echo "unpacker appears to have produced no directories";`
`          exit 1;`
`      fi;`
`      echo "source root is $sourceRoot";`
`      if [ "$dontMakeSourcesWritable" != 1 ]; then`
`          chmod -R u+w "$sourceRoot";`
`      fi;`
`      runHook postUnpack`
`  }`

you can also modify the configureFlags prefix:

`  export configureFlags="--prefix=$out --with-readline"`

Tip: A git repository can be used for snapshotting attempts at building the package. This also makes it easy to generate patches, should you need to.

nix channels
------------

nix channels can be used in parallel with your new local repositories, see its [nix-channel-documentation](/install/remove_software#nix-channels "wikilink")

Testing Package Updates with Nox
--------------------------------

If you are updating a package's version, you can use nox to make sure all packages that depend on the updated package still compile correctly.

First make sure it is in your environment:

`   nix-env -iA nixpkgs.nox`

You can run nox against uncommitted changes to a nixpkgs repository:

`   cd CLONE_OF_NIXPKGS`
`   nox-review wip`

Test your uncommitted change against latest master:

`   cd CLONE_OF_NIXPKGS`
`   git fetch origin`
`   nox-review wip --against origin/master`

If you have already committed your changes and created a pull request, you can use the pr command:

`   nox-review pr 5341`

Checking run-time dependencies
------------------------------

First check if everything works at build-/compile-time. Refer to section [Create_and_debug_nix_packages\#Using_nix-shell_for_package_development](/Create_and_debug_nix_packages#Using_nix-shell_for_package_development "wikilink"), pointing the path argument to the root directory of your nixpkgs fork.

Check if your application runs and that your package is not missing any run-time dependencies. Install it in a clean environment from the root directory of your nixpkgs fork

`   $ nix-shell -I nixpkgs=. --pure -p my_great_package`

Run your application and see if any errors appear. If there are errors make a [Nix_Runtime_Environment_Wrapper](/Nix_Runtime_Environment_Wrapper "wikilink").

[Category:Development](/Category:Development "wikilink") [Category:NixOS](/Category:NixOS "wikilink") [Category:Nixpkgs](/Category:Nixpkgs "wikilink")