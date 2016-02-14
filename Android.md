---
title: Android
permalink: /Android/
---

Sander van der Burg announced this:

-   <http://sandervanderburg.blogspot.com/2012/11/building-android-applications-with-nix.html>
-   <http://sandervanderburg.blogspot.nl/2013/08/some-improvements-to-nix-android-build.html>

Summary: The Android SDK is mostly packaged; the source seems to only be available through Git, so only the binaries are packaged. 'android update' does not work so don't use it. Sander van der Burg does not use Android Studio so do not expect that to work either (although it has been packaged). The packaging uses XSL to translate Google-provided XML files into Nix expressions. Each file is individually accessible. Code is at [1](https://github.com/NixOS/nixpkgs/tree/master/pkgs/development/mobile/androidenv), example is at [2](https://github.com/svanderburg/nix-androidenvtests).

There are two useful functions:

-   androidenv.buildApp
-   androidenv.emulateApp

Support for <http://www.android-x86.org/> has not been implemented yet.