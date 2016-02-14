---
title: Main Page Package Manager
permalink: /Main_Page_Package_Manager/
---

The Nix package manager installs, updates, and removes Nix-packaged apps and libraries. Nix is unique among package managers in that software installed by Nix is read-only, which ensures that a Nix package's dependencies will not change when upgrading or removing other packages. As a result of this design, the Nix package manager has several desirable side-effects, including atomic package upgrades/rollbacks and complete dependency graphs for every package. The [About Nix](http://nixos.org/nix/manual/#idp24368448) chapter of the Nix manual has more information.

This page was created by the Nix community to help others understand and use the Nix package manager. Please help by adding pages for missing topics and updating information in existing pages.

About
=====

-   [What is a Nix Package?](/Nix_Package "wikilink")

How to Use
==========

Basics
------

-   [Installing Packages](/Nix_Installing_Packages "wikilink")
-   [Creating Packages](/Nix_Creating_Packages "wikilink")
-   [Modifying Packages](/Nix_Modifying_Packages "wikilink")

Advanced
--------

-   [Advanced Package Management](/install/remove_software "wikilink")
-   [Advanced Package Searching](/Howto_find_a_package_in_NixOS "wikilink")
-   [How to keep multiple packages up to date at once](/Howto_keep_multiple_packages_up_to_date_at_once "wikilink") - (e.g. pkgs.misc.collection)
-   [How to unpack a package's sourcecode](/How_to_unpack_a_package's_sourcecode "wikilink")
-   [How to add large files to the nix-store](/How_to_add_large_files_to_the_nix-store "wikilink")
-   [Modifying Software](/modify_software "wikilink")

Platforms
=========

-   [CrossCompiling](/CrossCompiling "wikilink")
-   [Nix on Windows](/Nix_on_Windows "wikilink")

Languages
=========

-   [How to develop on NixOS](/Howto_develop_software_on_nixos "wikilink") - (NOT how to develop \*for\* NixOS or Nix)
-   [Haskell](/Haskell "wikilink")
-   [Python](/Python "wikilink")
-   [Ruby](/Ruby "wikilink")
-   [Android](/Android "wikilink")
-   [Plone development](/Plone_development "wikilink")

Issues
======

-   [Terms and Definitions in Nix Project](/Terms_and_Definitions_in_Nix_Project "wikilink")
-   [Error Messages](/Error_Messages "wikilink")
-   [Solve GConf errors when running GNOME applications (such as GnuCash)](/Solve_GConf_errors_when_running_GNOME_applications "wikilink")
-   [FAQ](/FAQ "wikilink")
-   [NixOS Project on GitHub](https://github.com/NixOS): Search for or create issues
