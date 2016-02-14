---
title: Using Oracle JDK instead of Open JDK
permalink: /Using_Oracle_JDK_instead_of_Open_JDK/
---

Almost all Java packages in nixpkgs use Open JDK in form of a **jre** dependency. If you use Oracle JDK and also want other applications to use it, you can simply tweak your **.nixpkgs/config.nix** so that your desired application uses Oracles JDK or JRE.

Example with UMLet with JRE 8:

` {`
`   allowUnfree = true;`
`   packageOverrides = pkgs: rec {`
`      umlet = pkgs.umlet.override {`
`        jre = pkgs.oraclejre8;`
`      };`
`   ;`
` }`