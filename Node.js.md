---
title: Node.js
permalink: /Node.js/
---

Node.js is an OS-level JavaScript runtime, which is often used to run server-side JavaScript apps, like Express.js, and browser-targeted app tooling, like Grunt.

This page introduces ways of using Nix to help when using or developing Node.js apps.

This page is just starting, so content is still sparse.

Use Nix in Node.js App Development
==================================

Nix has tools for managing shell environments and resolving dependencies declared in a package description. As Node.js developers, we can use these facilities in both our development workflow and our deployment process. The following sections walk through these workflows.

Development
-----------

There are a few challenges of Node.js app development for which we rely on tools. Nix provides a good solution to some of these challenges, which we will explain and highlight below.

### Library Resolution

As a Node.js developer, we traditionally use NPM to fetch and properly install third-party libraries, which we then simply reference and use in our app.

(needs more content - explain how to resolve Node libraries/modules using Nix. Perhaps npm2nix and shell.nix?)

#### Short npm2nix usage explanation

-   Ensure npm2nix is available for use. One way is to install via nixpkgs -- nodePackages.npm2nix.
-   Add a line to nixpkgs/pkgs/top-level/node-packages.json. Typically, the line will look something like: , "ionic" -- i.e. a comma followed by a string in double quotes.
-   In nixpkgs/pkgs/top-level/, run: npm2nix node-packages.json node-packages-generated.nix
-   After waiting for copious output to scroll by, try building: e.g. in the nixpkgs dir: nix-build -K -A nodePackages.ionic

**Caveat**

At the time of this writing, some packages have started to require npm 2.x (e.g. recent versions of yo), but building of such packages fails.

### Build Tool Installation

A Node.js app can become complex to build and manage as it grows. To manage this complexity, we rely on build tools, such as Grunt, a linter, or a transpiler. These tools are only using during development, so they need to be considered as a case different than library resolution.

(needs more content - explain how to include build tools in a shell.nix file.)

Deployment
----------

(needs content - conceptual overview of the problem, the traditional Node.js solution, and how Nix's answer.)

Community Content
=================

-   [Node.js on NixOS status](http://blog.lastlog.de/posts/nodejs_on_nixos_status/) (Sept 2014)
-   [Deploying NPM packages with the Nix package manager](http://sandervanderburg.blogspot.fr/2014/10/deploying-npm-packages-with-nix-package.html) (Oct 2014)
