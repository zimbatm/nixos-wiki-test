---
title: How to tweak Linux kernel config options
permalink: /How_to_tweak_Linux_kernel_config_options/
---

Modifying a particular kernel
-----------------------------

Here, we'll override `linux_3_18` with extra patches and kernel options (we could just as well create a new name instead of overriding - e.g. `linux_3_18_custom`).

` nixpkgs.config = {`
`   packageOverrides = super: let self = super.pkgs; in {`
`     linux_3_18 = super.linux_3_18.override {`
`       kernelPatches = super.linux_3_18.kernelPatches ++ [`
`         # we'll add the Ubuntu Fan Networking patches from Nixpkgs`
`         self.kernelPatches.ubuntu_fan`
` `
`         # and we'll also add one of our own patches`
`         { patch = ../patches/i915_317.patch; name = "i915-fix"; }`
`       ];`
` `
`       # add "CONFIG_PPP_FILTER y" option to the set of kernel options`
`       extraConfig = "PPP_FILTER y" ;`
`     };`
`   };`
` };`

Adding options to the mainstream kernel
---------------------------------------

Following config snippet asks Nix to add "CONFIG_PPP_FILTER y" option to the set of kernel options for current kernel. Of course, that would require Nix to rebuild the kernel locally.

` nixpkgs.config = {`
`   packageOverrides = pkgs: {`
`     stdenv = pkgs.stdenv // {`
`       platform = pkgs.stdenv.platform // {`
`         kernelExtraConfig = "PPP_FILTER y" ;`
`       };`
`     }; `
`   };`
` };`

Patching the kernel
-------------------

If you wish to apply patches to the kernel, you can do so using the kernelPatches option. The value of patch is set to the path to the patch file you are applying. For example, the following code snippet would apply two patches to the kernel.

` nixpkgs.config = {`
`   packageOverrides = pkgs: {`
`     stdenv = pkgs.stdenv // {`
`       platform = pkgs.stdenv.platform // {`
`         kernelPatches = [`
`           { patch = ../patches/i915_317.patch; name = "i915-fix"; };`
`           { patch = ../patches/override_for_missing_acs_capabilities.patch;`
`             name = "acs_overrides"; };`
`         ];`
`       };`
`     }; `
`   };`
` };`

Building the kernel with your own .config
-----------------------------------------

To build your a customized Linux kernel, place and adapt the following example in your '/etc/nixos/configuration.nix':


    boot.kernelPackages = pkgs.linuxPackages_custom {
      version = "3.18.1-custom";
      src = pkgs.fetchurl {
        url = "mirror://kernel/linux/kernel/v3.x/linux-3.18.1.tar.xz";
        sha256 = "13m0s2m0zg304w86yvcmxgbjl41c4kc420044avi8rnr1xwcscsq";
      };
      configfile = /etc/nixos/customKernel.config;
    };

configfile points to your custom Linux kernel config file obtained by e.g. make menuconfig within the Linux source.

------------------------------------------------------------------------

'''Note: The following is deprecated and can be deleted at some point. It is essentially a more verbose way to achieve what pkgs.linuxPackages_custom from above does.

**Note: this section describes non-trivial operations.**

**TODO: Rewrite the steps below assuming that nixpkgs tree is available read-only.**

In order to build the completely different kernel, you will need to write your own kernel expression.

-   Obtain the .config file you want to build the kernel with (do the make menuconfig).
-   Write the nix expression for the kernel you want to build. The expression may use the buildLinux function declared in all-packages.nix. My version looks like the following:

<!-- -->


    # File pkgs/my-linux.nix
    { stdenv, fetchurl, buildLinux } :

    buildLinux rec {

      version = "3.10.28";

      src = fetchurl {
        url = "mirror://kernel/linux/kernel/v3.x/linux-${version}.tar.xz";
        sha256 = "1blzvr3qywi8wxgl28zsn5djwgvw70yh3i6qjh2sz3zk9gnpd6mq";
      };

      configfile = /path/to/your/.config;

      allowImportFromDerivation = true;
    }

Change the src according to your needs.

-   Modify pkgs/all-packages.nix. Add the following lines pointing to your expression

<!-- -->

    my_linux = callPackage ../my-linux.nix { };
    my_linuxPackages = recurseIntoAttrs (linuxPackagesFor pkgs.my_linux my_linuxPackages);

-   Change the boot.kernelPackages option in your NixOS configuration file. Point it to pkgs.my_linuxPackages.

Note, that at the moment, .config has to have support for modules. See [this thread](http://lists.science.uu.nl/pipermail/nix-dev/2014-March/012663.html) for details