---
title: Keymap
permalink: /Keymap/
---

Changing your X keymap
----------------------

In your *configuration.nix* set the layout and the variant you want to use. If you want to use Programmer's Dvorak then you would use the **us** layout with **dvp** variety:

    services.xserver.layout = "us";
    services.xserver.xkbVariant = "dvp";

This follows the regular **setxkbmap** syntax. Rebuild and restart X. You can use setxkbmap for temporary solution.

If you're using a desktop environment such as KDE, you can probably use that to set your keymap but you'll have problems if you ever stop using that particular desktop environment.

### Using a graphical tool

If you have used configuration tools like the KDE system settings manager, you can retrieve the string to send to **services.xserver.xkbOptions** in your **configuration.nix** by invoking **setxkbmap -query** like this:

    $ setxkbmap -query
    rules:      evdev
    model:      pc101
    layout:     us
    options:    eurosign:e,terminate:ctrl_alt_bksp,compose:ralt

This also shows what to send to **xserver.layout** and **xserver.xkbVariant**.

### Compose key

Use **'xkbOptions**':

    services.xserver.xkbOptions = "compose:caps";

The above would override the Caps-Lock key with Compose key behaviour.

As usual, remember that you can use **nixos-option services.xserver** to check available options as well as **man configuration.nix**.

### Custom XKB configuration

If you expirienced problems with loading custom configuration via **xkbcomp**, usually indicated as

    $xkbcomp  -I$HOME/.xkb $HOME/.xkb/main.xkb
    Error:            Can't find file "evdev" for keycodes include
                      Exiting
                      Abandoning keycodes file "(null)"

Then you need to enable **'exportConfiguration**'

    services.xserver.exportConfiguration = true;

This option enable propagating **/etc/X11/xkb** symlink, which is standard include path for **xkbcomp**.

Changing your virtual console keymap
------------------------------------

If you don't boot straight into a graphical session, the chances are you'll want to change your keymap and possibly locale. You can do this with **i18n** block in your *configuration.nix*. The **kbd** package provides many keymaps for you to use

    i18n = {
      consoleKeyMap = "dvorak;
      defaultLocale = "en_US.UTF-8";
    };

Notably, **consoleKeyMap** can point to a file but you have to make sure it's available early on in the boot process (so don't put it on a network share you mount later). I point mine to a keymap in my home directory as it is not provided by **kbd** package.

    consoleKeyMap = "/home/shana/keymaps/dvp-1_2_1.map.gz";

The syntax is the same as the one accepted by **loadkeys** command (from **kbd** package)