---
title: Talk:NFS
permalink: /Talk:NFS/
---

The method described is a bit fragile. nixos-rebuild switch actually hanged for me because some directories were missing. Isn't it better to use systemd.mounts to manage the bind mounts?