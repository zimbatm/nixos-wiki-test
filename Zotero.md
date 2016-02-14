---
title: Zotero
permalink: /Zotero/
---

Zotero Firefox add-on
---------------------

The Zotero Firefox add-n works as expected with one caveat; the *pdftotext* and *pdfinfo* plugins it needs in order to index and pull metadata from PDFs you wish to add to your library.

The solution, for now, is as follows (this assumes that you have the Zotero plugin installed and configured sans PDF support):

1.  In your filesystem navigate to ~/.mozilla/firefox/<profile>/zotero
2.  Create the following symlinks and ensure that they are named EXACTLY as described here:

`  ln -s /run/current-system/sw/bin/pdftotext pdftotext-Linux-`<architecture>` (e.g., pdftotext-Linux-x86_64)`
`  ln -s /run/current-system/sw/bin/pdfinfo pdfinfo-Linux-`<architecture>

Launch Firefox and go to the Zotero add-on preferences; click on the "Search" tab and you should find that both, *pdftotext* and *pdfinfo*, are recognized and their version is "UNKNOWN". This is trivial and should not deter you. You can now use the PDF metadata retrieval capabilities.