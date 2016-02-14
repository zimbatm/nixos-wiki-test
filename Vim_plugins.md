---
title: Vim plugins
permalink: /Vim_plugins/
---

Vim plugins can be installed with the help of nix. You can omit using vim plugin managers and do everything in your `.nixpkgs/config`.

### Examples

#### Using VAM as manager

     vim_configurable.customize {
        name = "vim-with-plugins";
        # add custom .vimrc lines like this:
        vimrcConfig.customRC = ''
          set hidden
          set colorcolumn=80
        '';
        vimrcConfig.vam.knownPlugins = pkgs.vimPlugins; # optional
        vimrcConfig.vam.pluginDictionaries = [
          # load always
          { name = "youcompleteme"; }
          { names = [ "youcompleteme" "foo" ]; }
          # only load when opening a .php file
          { name = "phpCompletion"; ft_regex = "^php\$"; }
          { name = "phpCompletion"; filename_regex = "^.php\$"; }
          # provide plugin which can be loaded manually:
          { name = "phpCompletion"; tag = "lazy"; }
        ];
      };

Full documentation at [VAM homepage](https://github.com/MarcWeber/vim-addon-manager).

**Please note**, that *`name = "vim-with-plugins";`* is a name for you future vim executable. So you have to start it to open vim. That lets us have different "vims".

*`gvim`* can be started by *`vim-name -g`*

#### Using Pathogen as manager

There is a pathogen implementation as well, but its startup is slower and \[VAM\] has more feature.

        vimrcConfig.pathogen.knownPlugins = vimPlugins; # optional
        vimrcConfig.pathogen.pluginNames = [ "vim-addon-nix" "youcompleteme" ];

### Adding new plugins

-   Check <https://github.com/NixOS/nixpkgs/tree/master/pkgs/misc/vim-plugins>
-   Add your plugin to ./vim-plugin-names
-   Generate via `vim-plugin-names-to-nix`
-   If plugin needs additional code (for example: some dependencies, extra build steps etc.) then create a pull request to [vim2nix](https://github.com/jagajaga/vim-addon-vim2nix/tree/master/additional-nix-code). That will helps update plugins in the future.

### Real life examples

-   [Jagajaga’s config](https://github.com/jagajaga/my_configs/blob/master/.nixpkgs/common.nix)
