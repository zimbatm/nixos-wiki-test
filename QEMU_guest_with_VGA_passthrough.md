---
title: QEMU guest with VGA passthrough
permalink: /QEMU_guest_with_VGA_passthrough/
---

Introduction
------------

This article describes how to run a QEMU guest with VGA passthrough, using KVM. Passing through a GPU to the guest means that the guest takes complete ownership of the GPU. You need to either plug this graphics card to a secondary screen or another port on the host's monitor, so you can toggle between host and VM. If your hardware supports this, you can say goodbye to dual booting once and for all!

Prerequisites
-------------

-   VT-D (intel) or AMD-VI enabled in BIOS (and working). This requires a compatible motherboard and CPU. Notably, Intel's -K series processors seldom support VT-D.
-   At least 2 GPUs. One for the host, and one for the guest. Integrated GPU for the host is fine.
-   QEMU &gt;= 2.0 (tested on 2.2.1).
-   Linux kernel &gt;= 3.9 (tested on 3.19.7).

Host configuration
------------------

The NixOS installation process should have added either kvm-intel or kvm-amd to the system's kernel modules. Make sure that it's there in /etc/nixos/hardware-configuration.nix.

The computer in this example has a discrete AMD GPU and an Intel CPU with integrated graphics. This is an excerpt of the machine's configuration.nix:

`{ config, pkgs, ... }:`
`{`
`  boot.kernelModules = [`
`    "vfio"`
`    "vfio_pci"`
`    "vfio_iommu_type1"`
`  ];`
`  boot.blacklistedKernelModules = [ "radeon" ]; # or nouveau or nvidia or fglrx`
`  boot.kernelParams = [`
`    "intel_iommu=on" # AMD CPUs shouldn't need a line like this one`
`    "vfio_iommu_type1.allow_unsafe_interrupts=1"`
`    "kvm.allow_unsafe_assigned_interrupts=1"`
`    "kvm.ignore_msrs=1" # This prevents certain (BSOD) crashes in Windows guests.`
`#    "i915.enable_hd_vgaarb=1"`
`  ];`
`  # ... usual configuration ...`
`}`

Blackisting a graphics driver is only one way of preventing the GPU to be loaded by NixOS. This is not always an option. Other approaches, as well as a lot more detail about various ways to make all of this work (in Arch Linux), can be found in this post: [KVM VGA-Passthrough using the new vfio-vga support in kernel =&gt;3.9](https://bbs.archlinux.org/viewtopic.php?id=162768).

Binding a device to vfio-pci
----------------------------

After rebuilding and rebooting with this new config, there's one thing that you need to do before QEMU can pass through your GPU. You need to rebind the GPU (and GPU audio) to vfio-pci, and it resets on every boot. First, find their identifier (for lack of a better word):

`$ lspci`
`...`
`01:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Cayman XT [Radeon HD 6970]`
`01:00.1 Audio device: Advanced Micro Devices, Inc. [AMD/ATI] Cayman/Antilles HDMI Audio [Radeon HD 6900 Series]`
`...`

In this example, 01:00.0 and 01:00.1 should be rebound. Take note of these identifiers. They are used both for the rebinding and passed to KVM. A reusable shell script function for rebinding to vfio-pci is right at the top of the script in the next section. You may prefer to move it into its own script and execute it via systemd.

Running the VM with a script
----------------------------

You need to install one of "kvm" or "qemu" from nixpkgs. Then, a minimal script rebinding the GPU and running QEMU with VGA passthrough looks something like this:

`vfiobind() {`
`    dev="$1"`
`    vendor=$(cat /sys/bus/pci/devices/$dev/vendor)`
`    device=$(cat /sys/bus/pci/devices/$dev/device)`
`    if [ -e /sys/bus/pci/devices/$dev/driver ]; then`
`        echo $dev > /sys/bus/pci/devices/$dev/driver/unbind`
`    fi`
`    echo $vendor $device > /sys/bus/pci/drivers/vfio-pci/new_id`
`}`
`vfiobind 0000:01:00.0`
`vfiobind 0000:01:00.1`
`qemu-kvm \`
`-m 8G \`
`-cpu host \`
`-smp sockets=1,cores=3,threads=2 \`
`-device pci-assign,host=01:00.0 \`
`-device pci-assign,host=01:00.1 \`
`vmImage.img`

You may want to add the -nographic flag once you know that everything works. Then you no longer get a window on your host (supported by an emulated GPU) where you can operate the VM. But you will want to have that window for installing the OS, and the GPU driver (if it needs to be done manually), before the passed through graphics output has kicked in. Once it finally does, the secondary monitor behaves just like a monitor for another physical machine. Also, with -nographic, you can no longer get the keyboard and mouse capture that you would get by clicking on the window. A secondary keyboard & mouse or some software like [Synergy](http://synergy-project.org) are viable options.

You can create a systemd service that runs the script on boot-up. But then the -nographics flag must be set.

Install the guest OS on a separate disk
---------------------------------------

This step is completely optional. If you want the best performance from the VM, and you want to blur the lines between a virtual machine and bare metal one step further, read on.

The disk argument to qemu-kvm doesn't have to be a disk image, like the vmImage.img file in the example above. You can specify a physical disk there instead, for example "/dev/sdb". A disk partition is also possible, but more difficult to get right, because then you have to deal with the fact that QEMU treats it as a disk and wants to put a partition table in there. If you do this you will actually be able to boot straight into the disk, if you wanted to for some reason. At that point, you would be hard pressed to call it a VM image.

In a tested case with a Windows guest, it doesn't even complain and prompt for reactivation when switching between booting it from QEMU or bare metal. Windows is known to do that when hardware changes are too big between boots. But with so many real parts passed through with KVM, it doesn't seem to trigger! So in theory, existing dual boot setups could be converted into a VM like this.

There's a possibility to enable UEFI support for QEMU and KVM using [OVMF](http://www.tianocore.org/ovmf/).

References
----------

-   [KVM VGA-Passthrough using the new vfio-vga support in kernel =&gt;3.9](https://bbs.archlinux.org/viewtopic.php?id=162768)

See also
--------

[QEMU guest with networking and virtfs](/QEMU_guest_with_networking_and_virtfs "wikilink")

[Category:Virtualization](/Category:Virtualization "wikilink")