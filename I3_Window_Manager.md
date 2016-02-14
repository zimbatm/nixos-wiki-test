---
title: I3 Window Manager
permalink: /I3_Window_Manager/
---

Installation
------------

You'll have to make the following changes to your `/etc/nixos/configuration.nix`.

First, add this line.

    services.xserver.windowManager.i3.enable = true;

Then look for the `environment.systemPackages` section and add `pkgs.i3status` and `pkgs.dmenu` between the brackets. It should look something like this:

    environment.systemPackages = [ pkgs.i3status pkgs.dmenu ];

Finally, there is no default i3status.conf provided by NixOS, so i3status will just show an error if you try to use i3 now. Create a new file named `.i3status.conf` in your home directory ~, then add the following text to it:

    general {
            output_format = "i3bar"
            colors = true
            interval = 5
    }

    order += "ipv6"
    order += "disk /"
    order += "run_watch DHCP"
    order += "run_watch VPNC"
    order += "path_exists VPN"
    order += "wireless wlan0"
    order += "ethernet eth0"
    order += "battery 0"
    order += "cpu_temperature 0"
    order += "load"
    order += "tztime local"
    order += "tztime berlin"

    wireless wlan0 {
            format_up = "W: (%quality at %essid, %bitrate) %ip"
            format_down = "W: down"
    }

    ethernet eth0 {
            # if you use %speed, i3status requires the cap_net_admin capability
            format_up = "E: %ip (%speed)"
            format_down = "E: down"
    }

    battery 0 {
            format = "%status %percentage %remaining %emptytime"
            format_down = "No battery"
            status_chr = "⚇ CHR""
            status_bat = "⚡ BAT"
            status_full = "☻ FULL"
            path = "/sys/class/power_supply/BAT%d/uevent"
            low_threshold = 10
    }

    run_watch DHCP {
            pidfile = "/var/run/dhclient*.pid"
    }

    run_watch VPNC {
            # file containing the PID of a vpnc process
            pidfile = "/var/run/vpnc/pid"
    }

    path_exists VPN {
            # path exists when a VPN tunnel launched by nmcli/nm-applet is active
            path = "/proc/sys/net/ipv4/conf/tun0"
    }

    tztime local {
            format = "%Y-%m-%d %H:%M:%S"
    }

    tztime berlin {
            format = "%Y-%m-%d %H:%M:%S %Z"
            timezone = "Europe/Berlin"
    }

    load {
            format = "%5min"
    }

    cpu_temperature 0 {
            format = "T: %degrees °C"
            path = "/sys/devices/platform/coretemp.0/temp1_input"
    }

    disk "/" {
            format = "%free"
    }

This is the default i3status.conf from the man page.

Now all you have to do is `nixos-rebuild switch` and then restart the display manager by entering

<li>
systemctl restart display-manager.service

</li>
with root permissions in a terminal.

[Category:Services](/Category:Services "wikilink") [Category:Desktop environments](/Category:Desktop_environments "wikilink")