---
title: Gitit
permalink: /Gitit/
---

[Gitit](http://www.gitit.net/) is a wiki backed by a git, darcs, or mercurial filestore. Pages and uploaded files can be modified either directly via the VCS’s command-line tools or through the wiki’s web interface. Pandoc is used for markup processing, so pages may be written in (extended) markdown, reStructuredText, LaTeX, HTML, or literate Haskell, and exported in ten different formats, including LaTeX, ConTeXt, DocBook, RTF, OpenOffice ODT, and MediaWiki markup.

As NixOS Service
----------------

Gitit has a very feature rich integration in NixOS, just see:

`   $NIXPKGS/nixos/modules/services/misc/gitit.nix`

And visit <http://nixos.org/nixos/options.html> and enter gitit there.

Started manually
----------------

first we need to install it:

`   nix-env -f "`<nixpkgs>`" -i -A haskellPackages.gitit -Q`

later you can start it by typing:

`   gitit`

further help is provided by:

`   gitit --help`
`   gitit --debug`