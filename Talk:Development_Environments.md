---
title: Talk:Development Environments
permalink: /Talk:Development_Environments/
---

neither of these example commands work:

$ nix-shell --command /bin/sh nvi $ nix-shell nvi

Probably because nvi is not installed on my system, I guess? Nor does:

$ nix-shell --command /bin/sh firefox error: getting status of \`/home/Ansible/firefox': No such file or directory