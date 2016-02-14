---
title: WeeChat
permalink: /WeeChat/
---

*WeeChat* is an extensible chat client with a command line interface.

Plugins
-------

WeeChat can be extended with plugins, which can be written in a variety of scripting languages. As these plugins may depend on external libraries, we need to take care that those are found by WeeChat. For example the [Jabber](https://weechat.org/scripts/source/jabber.py.html/) plugin depends on the `xmpppy` python library, which is provided in a separate nix package. To make WeeChat find the library, we can override the `weechat` nix package and add the dependency as an extra build input, e.g. by changing the `~/.nixpkgs/config.nix` as follows:

    {
      packageOverrides = pkgs: rec {
        weechat = pkgs.weechat.override { extraBuildInputs = [ pkgs.xmpppy ]; };
      };
    }

If the `weechat` package is then reinstalled, it is build from source including the `xmpppy` library.