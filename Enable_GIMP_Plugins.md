---
title: Enable GIMP Plugins
permalink: /Enable_GIMP_Plugins/
---

On NixOS:

The following configuration will install GIMP and the G'MIC plugin:

`   environment = {`
`    systemPackages = [ pkgs.gimp_2_8 pkgs.gimpPlugins.gmic ];`
`    pathsToLink = [ "/${pkgs.gimp.name}-plugins" ];`
`  }`

After running \`nixos-rebuild switch\` you should have the following path on your system: /run/current-system/sw/gimp-2.8.10-plugins/ and it should contain \`gmic_gimp\`.

GIMP must still be configured to find this path:

Add the following paths to Preferences-&gt;Folders-&gt;Plug-ins:

`   /run/current-system/sw/lib/gimp/2.0/plug-ins`
`   /run/current-system/sw/gimp-2.8.10-plugins`

{Image:gimp-plugins-path.png}

Note: when the version number of the GIMP changes, you'll need to update these paths to match.