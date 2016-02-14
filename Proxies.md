---
title: Proxies
permalink: /Proxies/
---

In order to use a proxy you must first switch to a configuration that has proxies enabled. This is done by adding:

`
 networking.proxy.default = "http://192.168.1.1:8118"
`

to your `/etc/nixos/configuration.nix` and running:

`
 nixos-rebuild switch
`

You can in the same fashion unset the proxy upon next activation by removing above attribute from your `configuration.nix`. You will not want to update any build expressions, or make drastic updates to your `configuration.nix`, since your last activation while setting or unsetting the proxy as this will likely make the needed set of rebuilt derivations larger.

### Intermediate proxy

If you have arrived at a network and haven't prepared for the absence or presence of a proxy you will need to perform some manual shell calls in order to switch over your system configuration. Unfortunately it's the pure activation behaviour of NixOS that introduces the proxy chicken and egg problem:

1.  you will not be able to set a proxy, since you're behind one and will timeout.
2.  you will not be able to unset a proxy, since you're not behind it and will timeout.

We manipulate the builder environment by building everything as root and not through the nix-worker, it will only see the environment as defined in the last activation.

#### Shell

Switch over using the following:

`
 su -
 export http_proxy="http://192.168.1.1:8118"
 export NIX_CURL_FLAGS="-x $http_proxy"
 export NIX_REMOTE=""
 nix-build -A system "<nixpkgs/nixos>" --option use-binary-caches false
 nixos-rebuild switch --option use-binary-caches false
`

#### Explanation

In the code above we:

1.  Switch to root (can also be done with `sudo su -`).
2.  Set the standard http_proxy environment variable that most unix programs looks for.
3.  Tell curl to use the proxy set in http_proxy for fetching, this step shouldn't be needed.
4.  Disable building through the nix daemon user and instead use yourself(root) as builder.
5.  Rebuild the system derivation, disabling binary-caches.
6.  Switch to the newly built system derivation, without checking binary-caches.

Your own proxy
--------------

If you want to make your life miserable, typically for testing, you can construct your own LAN proxy by installing privoxy onto your router and adding the following rule to it:

`
 iptables -A FORWARD -i $lif -p tcp -m multiport --dports 80,443 -j REJECT
`

Where `$lif` is the LAN interface name. You can also change `REJECT` to `DROP` if you prefer waiting, the former is better for testing and the latter for security. You will now, of course, need to connect through the privoxy instance via http_proxy.