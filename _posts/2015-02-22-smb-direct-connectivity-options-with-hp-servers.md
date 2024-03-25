---
title: SMB Direct connectivity options with HP servers
date: 2015-02-22T18:08:28+00:00
dsq_thread_id:
  - "4739217518"
classes: wide
tags:
  - HP
  - NVGRE
  - RDMA
  - SMB Direct
  - Windows Server
---

### Are there any 10GBE network cardsÂ from HP which you can use for SMB Direct?

Recently I was doing some research about the options of 10GBE/RDMA NICs for HP ProLiant rack servers (DL380/DL360) and I found some interesting new option for the relatively new Gen9 servers:

* **HP FlexFabric 10Gb 2-port 556FLR-SFP+ Adapter**
* **HP StoreFabric CN1200E 10-Gbit-Converged Network Adapter**  
A new Emulex XE-102 based NIC two 10GBE Ports which supports RDMA (RoCE) and NVGRE offload.
* **HP InfiniBand FDR/Ethernet 10Gb/40Gb 2-port 544FLR-QSFP Adapter**  
A HP branded version of the Mellanox ConnectX-3 PRO card which also supports RoCE and NVGRE offload and can also used for 40GBE or InfiniBand connection. This one seams also to support the Gen8 server.

The 556FLR und 544FLR are FlexFabric adapters. These are special option of ProLiant rack server to extend the onboard connectivity with additional network ports together with the four builtin 1Gbit/s onboard NICs. The CN1200E is a regular PCI-E for installation in normal PCI-E slot.

So the answer is yes, there are now some NICs available from HP which you can use for SMB Direct (and with the NVGRE offload abilities even for efficient VM connectivity with NVGRE).

### Why are the HP branded adapters interesting?

Sure you could use any RDMA capable NIC without the HP branding in a HP server but the benefit with HP branded adapter is that you get firmware and driver upgrades included with HP SUM like for any other components of the HP severs. So you can update all drivers and firmwares with one Tool/Setup at once.
  
Furthermore you have not to debate with HP that its not the network adapter which is responsible for the issue if you have a hardware support case for something else at HP. You know what I mean... 😉

## But no iWARP with HP?

It looks like HP has currently no love for iWARP. There are no HP branded NIC which supports iWARP or at least I could not found it. This meaning if you want to use HP Adapters for SMB Direct you have to go the RoCE path which need some more investments in the networking part (DCB/PFC). MVP [Didier Van Hoye has some great blog posts](https://workinghardinit.wordpress.com/2013/05/03/smb-direct-roce-does-not-work-without-dcbpfc/){:target="_blank"} about this topic.

## Update April 1, 2015:
There are now two new Mellanox ConnectX-3 Pro based adapters for Gen9 servers available:

* HP Ethernet 546SFP+ adapter (FlexFabric Adapater)
* HP Ethernet 546FLR-SFP+ adapter (PCI-E Adapter)

Thanks to the Mellanox chip the new adapters support both RDMA (RoCE) and NVGRE offloading like the older 544FLR but at a much lower price.
Although the 544FLR adapter supports also 40GBE and infiniBand. The new 546 adapters support "only" 10GBE.
