---
title: OpenSSH
permalink: /OpenSSH/
---

OpenSSH
=======

Installation
------------

`nix-env -i openssh`

Tips and Tricks
---------------

### Key based authentication

`ssh-keygen -t rsa`
`ssh-copy-id -i id_rsa.pub login@target.com`