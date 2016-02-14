---
title: Talk:Ruby
permalink: /Talk:Ruby/
---

Ruby on Nix Workflow
--------------------

### "The Nix Way" - Forget Gems and RVM

I think we need to discuss a proper workflow for using Ruby on Nix. There are many ways, but I am interested in "The Nix Way", which means using Nix to manage dependencies. If you have a different idea for a workflow, make a sibling section and introduce it there.

I think that Nix is a great complement for Ruby. If Nix is used, many Ruby tools could be deprecated, possibly.

-   Ruby versions
    -   Problems with Gems motivated the creation of RVM and RbEnv, which enables one to hide/show Gems for a specific Ruby version, and maybe for directory or project. If Nix doesn't solve this problem inherently, we could use Nix Profiles/User Environments.
-   Ruby libraries
    -   Gem packages have problems with dependency management. This is related to dependencies of dependencies and maybe overwriting Gems with minor version updates.
-   Ruby apps
    -   Package with Nix expressions and add to NixPkgs. This enables them to be added to an app's dependency chain.

If I'm missing an important aspect of Ruby development please add it.

[Chexxor](/User:Chexxor "wikilink") ([talk](/User_talk:Chexxor "wikilink")) 03:19, 18 November 2013 (CET)

Ruby on Nix Workflow
--------------------

Test add topic.