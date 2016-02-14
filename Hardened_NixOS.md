---
title: Hardened NixOS
permalink: /Hardened_NixOS/
---

This page describes the on-going work to provide a more secure version of NixOS, similar in goals to the Hardened Gentoo project ([wiki page 1](http://wiki.gentoo.org/wiki/Hardened_Gentoo), [wiki page 2](https://wiki.gentoo.org/wiki/Project:Hardened)), but applied to the base NixOS system instead.

Developed features
------------------

Currently, no feature is completely developed and ready to be used in production.

If you are interested in developing and/or contributing, please continue reading.

Features in development
-----------------------

### grsecurity/PaX

[grsecurity](https://grsecurity.net/) is a Linux kernel patch which adds a large number of optional security enhancements to both the kernel itself and userspace programs. Its main focus is to prevent the successful execution of known and unknown exploits. It also has its own optional Role-Based Access Control mechanism (RBAC), which is similar in goals to [AppArmor](http://en.wikipedia.org/wiki/AppArmor) and [SELinux](http://en.wikipedia.org/wiki/Security-Enhanced_Linux).

You can read a full, and quite extensive, list of grsecurity's features on the [Configuration Options chapter](https://en.wikibooks.org/wiki/Grsecurity/Appendix/Grsecurity_and_PaX_Configuration_Options) of the [Grsecurity Wikibook](https://en.wikibooks.org/wiki/Grsecurity).

Note that this new feature of NixOS is still a work in progress and there are known unfixed issues (detailed below). Please do not use it unless you expect breakage, and wish to contribute with bug reports or wish to develop it further.

#### How to use grsecurity

As of November 2013, grsecurity has 2 available versions:

-   a stable patch for kernel 3.2
-   a test patch for kernel 3.11

The following excerpt from a NixOS `configuration.nix` file shows how to enable the stable grsecurity patch:

       nixpkgs.config = {
         grsecurity = true;

         packageOverrides = pkgs: {
           linuxPackages = pkgs.linuxPackages_3_2_grsecurity;

           stdenv = pkgs.stdenv // {
             platform = pkgs.stdenv.platform // {
               kernelExtraConfig = ''
                 XEN n
                 HIBERNATION n
                 DEVKMEM? n
                 GRKERNSEC y
                 GRKERNSEC_CONFIG_AUTO y
                 GRKERNSEC_CONFIG_DESKTOP y
                 GRKERNSEC_CONFIG_VIRT_HOST y
                 GRKERNSEC_CONFIG_VIRT_EPT y
                 GRKERNSEC_CONFIG_VIRT_KVM y
                 GRKERNSEC_CONFIG_PRIORITY_SECURITY y
                 GRKERNSEC_PROC_USER y
                 GRKERNSEC_PROC_GID 0
                 GRKERNSEC_CHROOT_CHMOD n
                 GRKERNSEC_SYSCTL n
               '';
             };
           };
         };
       };

#### Configuration Tips

In order to properly configure your kernel, please read grsecurity's [Configuration Options chapter](https://en.wikibooks.org/wiki/Grsecurity/Appendix/Grsecurity_and_PaX_Configuration_Options).

-   There are 2 main ways of configuring `grsec`:
    -   `GRKERNSEC_CONFIG_AUTO y`, which tries to set up some reasonable defaults
    -   `GRKERNSEC_CONFIG_CUSTOM y`, which disables everything, forcing you to manually enable all necessary features

##### Using Auto Configuration

Unless you wish to enable only a few features, or you are very persistent and wish to configure all options one-by-one, `GRKERNSEC_CONFIG_AUTO y` is probably better. When using the Auto option, you must also specify all of the following options:

-   Select `GRKERNSEC_CONFIG_SERVER y` or `GRKERNSEC_CONFIG_DESKTOP y` to specify whether your system is a server or desktop.
-   Select `GRKERNSEC_CONFIG_VIRT_NONE y`, `GRKERNSEC_CONFIG_VIRT_GUEST y`, or `GRKERNSEC_CONFIG_VIRT_HOST y` to specify whether your system doesn't use virtualization, is a guest, or is a virtualization host, respectively.

> If you use virtualization, select `GRKERNSEC_CONFIG_VIRT_EPT y` or `GRKERNSEC_CONFIG_VIRT_SOFT y` to specify whether your CPU has hardware virtualization extensions, respectively.
>
> If you use virtualization, select `GRKERNSEC_CONFIG_VIRT_XEN y`, `GRKERNSEC_CONFIG_VIRT_VMWARE y`, `GRKERNSEC_CONFIG_VIRT_KVM y`, or `GRKERNSEC_CONFIG_VIRT_VIRTUALBOX y` to specify the virtualization software you use.

-   Select `GRKERNSEC_CONFIG_PRIORITY_PERF y` or `GRKERNSEC_CONFIG_PRIORITY_SECURITY y` to specify whether performance or security is the highest priority, respectively.

##### Other Configuration Notes

Disable the XEN, HIBERNATION, and DEVKMEM features to allow grsecurity to use additional, and important, security enhancements, so please explicitly disable them if you don't use them.

**You must** use `GRKERNSEC_CHROOT_CHMOD n` in your kernel config if you wish to use Nix to build software on your system (most people do). This line is not needed if instead you build software using NixOps from another machine, and users of the target system don't need to install software using Nix.

**You must** use either `GRKERNSEC_SYSCTL n` or `boot.kernel.sysctl."kernel.grsecurity.grsec_lock" = 1;` in your `configuration.nix` file, to prevent an attacker from easily disabling grsecurity protections.

Overview of features
--------------------

The following list is a copy of some of the work that has been done on Ubuntu security, and the description of the features can be found on their wiki ([Ubuntu Security Features](https://wiki.ubuntu.com/Security/Features))

| Feature                            | NixOS 14.04                                                      |
|------------------------------------|------------------------------------------------------------------|
| No Open Ports                      | policy                                                           |
| Password hashing                   | sha512                                                           |
| SYN cookies                        | kernel (ubuntu: kernel & sysctl)                                 |
| Filesystem Capabilities            | kernel, but not used (ubuntu: kernel & userspace)                |
| Configurable Firewall              | ufw                                                              |
| Cloud PRNG seed                    | no (ubuntu: pollinate)                                           |
| PR_SET_SECCOMP                   | kernel                                                           |
| AppArmor                           | default off (2.8.3)                                              |
| SELinux                            | not used                                                         |
| SMACK                              | not used                                                         |
| Encrypted LVM                      | supported                                                        |
| eCryptfs                           | packaged, not supported                                          |
| Stack Protector                    | not by default (ubuntu: gcc patch)                               |
| Heap Protector                     | not by default (ubuntu: glibc)                                   |
| Pointer Obfuscation                | not by default (ubuntu: glibc)                                   |
| Stack ASLR                         | not by default (ubuntu: kernel)                                  |
| Libs/mmap ASLR                     | not by default (ubuntu: kernel)                                  |
| Exec ASLR                          | not by default (ubuntu: kernel)                                  |
| brk ASLR                           | not by default (ubuntu: kernel)                                  |
| VDSO ASLR                          | not by default (ubuntu: kernel)                                  |
| Built as PIE                       | (document list of services here..)                               |
| Built with Fortify Source          | no (ubuntu: gcc patch)                                           |
| Built with RELRO                   | no (ubuntu: gcc patch)                                           |
| Built with BIND_NOW               | (document list of services here..)                               |
| Non-Executable Memory              | no (yes with grsecurity?)                                        |
| /proc/$pid/maps protection         | yes, kernel                                                      |
| Symlink restrictions               | yes, sysctl default                                              |
| Hardlink restrictions              | yes, sysctl default                                              |
| ptrace scope                       | no (ubuntu: kernel, yama)                                        |
| 0-address protection               | sysctl (ubuntu: kernel)                                          |
| /dev/mem protection                | kernel                                                           |
| /dev/kmem disabled                 | kernel                                                           |
| Block module loading               | kernel (no config support)                                       |
| Kernel Read-only data sections     | kernel (with grsecurity at least)                                |
| Kernel Stack protector             | kernel (with grsecurity at least)                                |
| Module RO/NX                       | not set (ubuntu: kernel) (need CONFIG_DEBUG_SET_MODULE_RONX) |
| Kernel Address Display Restriction | enabled, but disabled under grsecurity                           |
| Blacklist Rare Protocols           | using the ubuntu list                                            |
| Syscall Filtering                  | in kernel, but not utilized by default                           |
| Block kexec                        | sysctl, not integrated in config                                 |

