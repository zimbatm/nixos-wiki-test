---
title: How to update when Nix is too old to evaluate Nixpkgs
permalink: /How_to_update_when_Nix_is_too_old_to_evaluate_Nixpkgs/
---

If you're here because of an error message, run:

` curl -L `[`http://hydra.nixos.org/job/nixos/trunk-combined/nixpkgs.nix.x86_64-linux/latest-finished/nix/pkg/nix-1.8-x86_64-linux.nixpkg`](http://hydra.nixos.org/job/nixos/trunk-combined/nixpkgs.nix.x86_64-linux/latest-finished/nix/pkg/nix-1.8-x86_64-linux.nixpkg)` \`
`   | nix-install-package --non-interactive -`

To find out why read below:

The latest version of Nixpkgs may use features which are not available in our version of Nix. The result of this is being unable to install or reference packages from Nixpkgs. For example, the current Nixpkgs uses features of Nix 1.8, but we may only have Nix 1.7. To resolve this situation, we must acquire a newer version of Nix.

### Upgrading NixOS

`nixos-rebuild` automatically fetches and uses the latest version of Nix (as long as you don't use the `--fast` option), so no special action is required.

### Upgrading Nix

To upgrade Nix, we must obtain the newer version's binaries. There are two easy ways to do this, both of which fetch the binaries directly from the Nix project's build server. This method doesn't require evaluating Nixpkgs, so it works great for upgrading the Nix program, itself.

#### Installing via Browser

If we have a browser installed on the target platform, we can open the page for the latest nix available for our platform [x86_64](http://hydra.nixos.org/job/nixos/trunk-combined/nixpkgs.nix.x86_64-linux/latest-finished), [i686](http://hydra.nixos.org/job/nixos/trunk-combined/nixpkgs.nix.i686-linux/latest-finished) hosted by the Nix project's build server, and click the "One-click Install" link. This links to a file having a `.nixpkg` file extension, which should be set up to be handled by the `nix-install-package` program.

#### Installing via Command Line

If the platform doesn't have a browser installed, which is a common case for servers, we can directly use the `nix-install-package` program.

Paste the following command to install the latest version of the Nix program directly from the Nix project's build server.

` curl -L `[`http://hydra.nixos.org/job/nixos/trunk-combined/nixpkgs.nix.x86_64-linux/latest-finished/nix/pkg/nix-1.8-x86_64-linux.nixpkg`](http://hydra.nixos.org/job/nixos/trunk-combined/nixpkgs.nix.x86_64-linux/latest-finished/nix/pkg/nix-1.8-x86_64-linux.nixpkg)` \`
`   | nix-install-package --non-interactive -`

##### Trusted Binary Caches

The recommended method for upgrading Nix is by using the `nix-install-package` program. This program uses the "nix.conf" file to verify that the URL is included in the "trusted_binary_caches". Running `nix-install-package` as root is not constrained in this manner, but it will only install the specified package for the root user, rather than for our normal user account.

Therefore, if our installation is rejected due to being hosted by an untrusted host, we must add the hostname to the "nix.conf" file. For NixOS users, add the hostname to the `nix.trustedBinaryCaches` configuration option and rebuild the machine. For users of other OSs, manually edit the "nix.conf" file, usually found in the `/etc/nix/` directory.

### Developers tips

Sometimes developers use their own nixpkgs trees.

-   nixos-rebuild reads _NIXOS_REBUILD_REEXEC variable. Setting it to non-zero instructs system-wide nixos-rebuild to re-exec it's version from the developer's tree.
