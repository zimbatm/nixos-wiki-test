---
title: Talk:NixOS on Amazon EC2
permalink: /Talk:NixOS_on_Amazon_EC2/
---

The instructions here don't work:

From a fresh nixos using ami-2905196c

1.  nix-channel --update

( not documented here but apparently this is necessary to see any packages at all)

1.  nix-env -i ec2-api-tools

error: selector ‘ec2-api-tools’ matches no derivations

How does one actually install the ec2 tools?