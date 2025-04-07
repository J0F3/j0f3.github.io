---
title: The connection between Hyper-V Network Virtualization (NVGRE) and MTU Size (and Linux)
date: 2015-04-26T15:04:37+00:00
dsq_thread_id:
  - "4739411347"
tags:
  - HNV
  - Hyper-V
  - NVGRE
  - Windows Server
  - Windows Server 2012 R2
---

In a network with Hyper-V Network Virtualization (using NVGRE encapsulation) the MTU (Maximum Transmission Unit) size is 42 Bytes smaller than in a traditional Ethernet network (where it is 1500 Bytes). The reason for this is the NVGRE encapsulation which needs the 42 Bytes to store his additional GRE Header in the packet. So the maximum MTU size with Hyper-V Network Virtualization is 1458 Bytes.

## The problem with Linux VMs

For VMs running Windows Server 2008 or newer this should not be a Problem because Hyper-V has a mechanism which lowers the MTU size for the NIC of the VM automatically if needed. (Documented on the [TechNet Wiki](https://social.technet.microsoft.com/wiki/contents/articles/23833.windows-server-2012-r2-network-virtualization-nvgre-mtu-reduction.aspx){:target="_blank"}).

But with VMs running Linux you could run in a problem because the automatically MTU size reduction seem to not function correctly with Linux VMs: [https://support.microsoft.com/en-us/kb/3021753/](https://support.microsoft.com/en-us/kb/3021753/){:target="_blank"}

This has the effect that the MTU size in the Linux VMs stays at 1500 and therefore you can experience some very weird connection issues.

## The Solution

So there are two options to resolve this issue:

* Set the MTU size for the virtual NICs of all Linux VMs manually to 1458 Bytes
* Enable Jumbo Frames on the physical NICs on the Hyper-V Hosts. Then the there is no need to lower the MTU size in the VMs.
* (wait for kernel updates for your Linux distribution which has the fix from KB3021753 implemented)