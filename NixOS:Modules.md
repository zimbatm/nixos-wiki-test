---
title: NixOS:Modules
permalink: /NixOS:Modules/
---

NixOS has modules to have non-intrusive extensions. Modules are made to avoid manipulating the core of NixOS while allowing the user to separate their `configuration.nix` file into multiple sub-file which are specific to a device or a service configuration. Such separation permits the creation of services which can be tested for a while before being merged in the main line of NixOS.

This page describes how you can create a module. If you want a pratical view, you should have a look into this small [tutorial](/NixOS:extend_NixOS "wikilink").

Vocabulary
----------

-   [Declaration](/NixOS:Declaration "wikilink"): All options which are used with the `mkOption` function are declared. Declared options should at least have a `description` attribute which contains a multiple-line text for describing the effect of the option.
-   [Definition](/NixOS:Definition "wikilink"): All options which are set to plain values are considered as definitions. The plain value may vary depending on the expected type of the option.
-   Use: All result of options which are used to compute values of other option are option usages.
-   [Properties](/NixOS:Properties "wikilink"): These are option definition modifiers. Properties may change the influence of an option definition in the computation of an option result.

To find all files which are defining or declaring an option, you may refer to the generated [NixOS manual](http://nixos.org/nixos/manual) or to the `man` page of `configuration.nix`.

Syntax
------

Modules are declared with the following syntax:

    {
      imports = [
        # list of path to other modules.
      ];

      options = {
        # attribute set of option declarations.
      };

      config = {
        # attribute set of option definitions.
      };
    }

Another syntax exists for cases where no option declaration are necessary:

    {
      require = [
        # list of path to other modules.
      ];

      # attribute set of option definitions.
    }

Both of the previous syntax can be refined with an attribute set argument added on top:

    {config, pkgs, ...}:

    {
      # ...
    }

The following arguments can be retrieved from this attribute set:

-   [`config`](/NixOS:config_argument "wikilink"): The configuration of the entire system.
-   `options`: All option declarations refined with all definition and declaration references.
-   `pkgs`: The attribute set extracted from the Nix package collection and enhanced with the `nixpkgs.config` option.
-   `modulesPath`: The location of the `module` directory of NixOS.

For more information, you should refer to NixOS sources, with more insight into \[<https://github.com/NixOS/nixos/blob/master/lib/eval-config.nix>|`nixos/lib/eval-config.nix`\].

More computation or organisation can be necessary to enhance modules. In which case, you can add a `let .. in ..` statement between the argument and the attribute set.

    {config, pkgs, ...}:

    let
      # intermediate result.
    in

    {
      # ...
    }

References
----------

-   [NixOS Documentation](http://nixos.org/nixos/docs.html)

Related Work
------------

-   Debian [Config::Model](http://wiki.debian.org/PackageConfigUpgrade):

target configuration upgrades by abstracting the option of the configuration. Each file is a tree structure where leaves are values defined with an interpreted type. The interpreters are defined for each meta-configuration files name \*.conf. Configuration files does not seems to interact with each other to make consistent configuration. They provide an UI for editing their configuration file.

[Category:Configuration](/Category:Configuration "wikilink") [Category:NixOS](/Category:NixOS "wikilink")