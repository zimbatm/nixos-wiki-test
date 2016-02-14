---
title: User:Viric
permalink: /User:Viric/
---

I use nixos.

One explanation I once gave on irc about nix: [1](http://nixos.org/irc/logs/log.20100130)

<viric>` I'd like to help.`
<viric>` So, you get that it works as a functional language.`
<viric>` Fine with that? or I may explain about it?`
<dufflebunk>` Yes. The problem for me is how the different files work together. all-packages is the top level "manifest" of all packages, and points to other files for the actual nix expressions which defile the package. Is that correct?`
<dufflebunk>` define, not defile`
<armijn>` yeah`
<dufflebunk>` The inherit statement for the packages in the all-packages.nix file is basically a list of dependencies? And a package is basically a set of of parameters?`
<armijn>` yeah, kinda...`
<armijn>` a package in its simplest form is just that`
<armijn>` but it can get a bit more complicated`
`tarbo (~me@unaffiliated/tarbo) joined #nixos.`
<viric>` dufflebunk: can you understand what is the result of "evaluating a nix expressino"?`
<viric>` expression`
<viric>` (I'm trying to remember what I understood worst)`
<viric>` you will find that idiom often: "evaluate a nix expression"`
<dufflebunk>` No`
<dufflebunk>` I would guess it's a list of files`
<viric>` mmm n`
<viric>` o`
<viric>` (We need a short story about nix in the wiki I guess :)`
<viric>` so`
<armijn>` expressino...that sounds like evaluated coffee`
<viric>` Evaluating a nix expression is done through 'nix-instantiate'`
<viric>` armijn: :D`
<viric>` dufflebunk: nix-instantiate can do more`
<viric>` dufflebunk: but it has "--eval-only" :)`
<viric>` imagine an expression like (it is not nix language, take it as a functional):   a=3; b=4; c=a+b;`
<viric>` And you ask "evaluate the value of c"`
<viric>` the result will be 7`
<viric>` The same goes with a nix expression`
<viric>` But nix identifiers (not variables, I'd say, because they don't vary in value along evaluation) can have different types: string, list or attribute set`
<viric>` When you ask for the value of "cmake" (you evaluate the nixpkgs expression wanting the final value for the identifier 'cmake'), for example, it gives you a string with the path to the cmake installation`
`tarbo (~me@unaffiliated/tarbo) left irc: Ping timeout: 258 seconds`
<viric>` Here, I still have not talked about how that ends up into files in disk.`
<viric>` dufflebunk: are you following? :)`
<dufflebunk>` Yes`
<viric>` Some of the values get assigned simply:  s = "mystring";`
<viric>` a = { b = "bstring"; c = "cstring"; }`
<viric>` x = [ "a" "b" "c" ];`
<viric>` Of course, identifiers get values from other identifiers as specified in the nix language`
<viric>` a = "a"; b = a; ...`
<dufflebunk>` right`
<viric>` (as it is a functional language, don't imagine that the evaluation happens in the order of writting. The order of evaluation is up to the evaluating program.`
<viric>` So, here come the files.`
<viric>` There is a special function: derivation`
<viric>` which wants an attribute set as parameter`
<viric>` Ah, before the 'derivation' function, you got about functions, right?`
<dufflebunk>` Yes`
<viric>` f = x: [ x x]; a = f 3;`
<viric>` (then, a evaluates to [ 3 3 ] )`
<viric>` so`
<viric>` 'derivation' wants an attribute set as parameter`
<viric>` f = derivation { builder = "/bin/sh"; args = "..."; system = "..."; myvar1 = "..." };`
<dufflebunk>` ok`
<viric>` This makes 'f' have two kind of values, depending on the context of evaluation. If 'f' has to be evaluated as an attribute set, it gets the values of the derivation function parameters. For example, "f.builder" evaluates to "/bin/sh"`
<viric>` But`
<viric>` if 'f' has to be evaluated as a string (some situations trigger this, you will see), it gets the value of the output path of the derivation.`
<viric>` So`
<dufflebunk>` that would be one point of confusion I had then`
<viric>` if nix-instantiate *not only* evaluates nix-expressions, it creates ".drv" files in the store`
<viric>` Those .drv files describe "how to build an output path, a derivation in nix jargon"`
<viric>` And that description includes what program to call (builder), with what arguments (args), an output path (environment variable "out" for the builder), and the rest of attributes of the 'derivation' function go as environment variables`
<viric>` Where do you have nixpkgs?`
<dufflebunk>` The default location /etc/nixos/nixpkgs`
<viric>` ok`
<viric>` So, you may ask:   nix-instantiate -A cmake /etc/nixos/nixpkgs`
<viric>` This will create the .drv file needed to build cmake. Or simply name it, if you already have it in the store path.`
<viric>` 'nixpkgs' is a big attribute set, where 'cmake' is one of its attributes.`
<viric>` '-A cmake' means: evaluate the attribute 'cmake', starting from the main expression (if it is an attribute set, as in the case of nixpkgs)`
<viric>` well, you see, *not only evaluate*, but also create the .drv file.`
<viric>` Ok`
<viric>` Then there are other nix programs`
<viric>` Ah`
<viric>` sorry`
<viric>` notice that the 'derivation' function call passes the output path to the builder in the $out environment variable. This means that it is determined *before* calling the builder.`
<dufflebunk>` yes`
<viric>` No hash result, no store path, depends on "what the builder does after running". All depends on the contents of the attribute set passed to derivation`
<viric>` Ok`
<viric>` So`
<viric>` There is nix-store`
<viric>` "nix-store -r" *realises* a derivation`
`tarbo (~me@unaffiliated/tarbo) joined #nixos.`
<viric>` "nix-store -r" understands the '.drv' language, and calls the builder, in a proper build environment (creating the build temporary directory in TMPDIR, calling the builder with the environment variables, ...)`
<viric>` Here came a new jargon word: realise a derivation`
<dufflebunk>` i.e. makes it real?`
<viric>` dufflebunk: I've never seen anything not real.`
<viric>` :)`
<viric>` It calls the builder`
`Action: dufflebunk hands viric the square root of -1`
<viric>` The builder, that will receive the $out environment variable, should care on creating the $out file or directory in the output path.`
<viric>` dufflebunk: If it is about real or imaginary number, no, it does not make it real. :)`
<viric>` The "nix-store -r", after calling the builder, also does some tasks: sets the timestamps of all files at that output path to 1 second after the epoch, sets proper file owner, permissions, ... and checks for substrings trying to determine what paths from the "buildInputs" remain as "runtime dependencies" (in contrast to build dependencies)`
<viric>` And of course, checks the exit value of the builder. If it is non zero, it is considered as a failed build, and the $out path that could have been created is marked as "invalid".`
<viric>` nix-store has a database of what store paths are valid and what not. They get valid after a builder returning 0.`
<viric>` nix-store cares on that.`
<viric>` So`
<viric>` There is then another program`
<viric>` nix-build`
<viric>` It calls "nix-instantiate" and "nix-store -r" adequately, so the "output path" of the attribute you ask gets *realised*, *realising* any needed derivations it may need.`
<viric>` "nix-build -A cmake /etc/nixos/nixpkgs" will call nix-instantiate, get the .drv files, and call nix-store -r to build them in the appropiate order.`
<viric>` In fact, a ".drv" file will have other ".drv" files as dependencies (building cmake will depend on building gcc, ...)`
<viric>` And nix-instantiate will generate all needed .drv files`
`tarbo (~me@unaffiliated/tarbo) left irc: Ping timeout: 260 seconds`
<viric>` And nix-store -r, asked to realise a .drv file, will realise all dependencies too.`
<viric>` Then there is nix-env`
<viric>` nix-env manages a special kind of 'derivation', not described in nixpkgs, but internally described in the nix programs: a profile`
<viric>` It takes a list of output paths as inputs, and its result is a directory with symlinks to all the files together from those output paths, pointing to the original output paths.`
<viric>` And nix-env manages 'generations' of those profiles, and so on.`
<viric>` And sets your ~/.nix-profile symlink pointing to whatever profile generation you have chosen.`
<viric>` your shell is configured with PATH=~/.nix-profile/bin:$PATH  and you know the rest of the story.`
<viric>` dufflebunk sleeps :)`
<dufflebunk>` Not sleeping`
<viric>` no questions?`
<dufflebunk>` just considering`
<viric>` In nixpkgs there is a special derivation, the 'stdenv'`
<viric>` You know that the 'derivation' function needs a 'builder' attribute in its attribute set parameter.`
<dufflebunk>` yes`
<viric>` And we don't use to write a builder script or program for all the derivations we have.`
<viric>` we have a *generic* builder.`
<dufflebunk>` yes`
<viric>` If you look at stdenv, you will see that it is a derivation (this means, a string *AND* an attribute set), which has an attribute member called mkDerivation`
<viric>` This member is a function, that if evaluated, ends up evaluating a 'derivation' function, with a builder previously set, with gcc, coreutils, ... appearing set in an environment variable, ...`
<viric>` The default builder set by stdenv.mkDerivation when it calls the 'derivation' function is the *generic builder`
<viric>` *`
<viric>` (nix jargon)`
<viric>` used unless you overwrite it in the attribute set parameter to mkDerivation`
`--- Sun Jan 31 2010`