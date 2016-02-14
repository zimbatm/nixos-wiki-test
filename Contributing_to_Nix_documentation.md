---
title: Contributing to Nix documentation
permalink: /Contributing_to_Nix_documentation/
---

Where do I find the docs?
-------------------------

The Nix documentation is on GitHub in the following repositories:

-   [Nix package manager](https://github.com/NixOS/nix/tree/master/doc)
-   [NixOS](https://github.com/NixOS/nixpkgs/nixos/doc)
-   [NixOps](https://github.com/NixOS/nixops)
-   [Hydra](https://github.com/NixOS/hydra)

How do I create a topic?
------------------------

Nix documentation uses DocBook 5, an XML markup language. For general information on how to work in DocBook, see [DocBook the Definitive Guide](http://www.docbook.org/tdg5/en/html/docbook.html).

You can use an existing topic as a basis for the new topic or create a topic from scratch.

Keep the following guidelines in mind when you create and add a topic:

-   Make sure to store the topic file in the same directory as the book file to which you want to add the topic. The book file is an XML file normally named `manual.xml`.
-   If you include multiple words in the file name, separate the words with a dash. For example: `installing-cli.xml`
-   Make sure that the `xml:id` value is unique. You can use abbreviations the ID is too long. For example: `nixos-config`
-   Determine whether your topic is a chapter or a section. If you are unsure, open an existing topic file and check whether the main element is `chapter` or `section`.

*Note: In the future you will be able to use a topic template. We welcome contributions to the template pool!*

How do I add a topic to a book?
-------------------------------

Open the parent XML file and add an `xi:include element` to the list of chapters with the file name of the topic that you created. If you created a section, you add the file to the chapter file. If you created a chapter, you add the file to the book file.

How do I build and test the manual?
-----------------------------------

Before you commit the new or updated content, you compile the source files and check that the generated output looks and behaves as expected. You use Nix to build the documentation, and each manual requires a slightly different process.

### Prerequisites

Before you build and test the output, you must install Nix on the machine where you store the local Git repo of the documentation.

### How to build the NixOS manual

1.  In a terminal, navigate to the `/nixpkgs/nixos` directory, where the `doc` directory is located.
2.  Run the following command: `$ nix-build release.nix -A manual.x86_64-linux`
3.  Open the generated output in a Web browser from the following path: `../nixos/result/share/doc/<project_name>/manual.html`

### How to build the Nixpkgs manual

1.  In a terminal, navigate to the `nixpkgs/doc` directory.
2.  Run the following command: `$ nix-build`
3.  Open the generated output in a Web browser from the following path: `../nixpkgs/result/share/doc/<project_name>/manual.html`
