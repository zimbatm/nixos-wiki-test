---
title: R
permalink: /R/
---

Installation
------------

Put into your $HOME/.nixpkgs/config.nix either

`   rToolsEnv = pkgs.buildEnv {`
`       name = "rTools";`
`       paths = with rPackages; [`
`           devtools`
`           ggplot2`
`       ];`
`     };`

or

`   rWrapper = super.rWrapper.override {`
`     packages = with self.rPackages; [ devtools ggplot2 ]`
`   };`

Developing R packages
---------------------

Put a default.nix into your project folder

` let`
`   pkgs = import `<nixpkgs>` {};`
`   buildRPackage = import <nixpkgs/pkgs/development/r-modules/generic-builder.nix> pkgs.R;`
` in`
` with pkgs.rPackages;`
` {`
`   foobar = buildRPackage {`
`     name = "your-package-name-1.0";`
`     src = ./.;`
`     propagatedBuildInputs = [/* required dependencies go here */];`
`   };`
` }`