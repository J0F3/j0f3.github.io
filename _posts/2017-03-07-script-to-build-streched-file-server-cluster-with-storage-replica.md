---
title: Script to build stretched file server cluster with Storage Replica
date: 2017-03-07T20:25:16+00:00
classes: wide
dsq_thread_id:
  - "5611353929"
header:
  teaser: /assets/images/SR_StretchedFileCluster.png
tags:
  - PowerShell
  - Storage Replica
  - Windows Server
  - Windows Server 2016
---

One possible scenario for use of Storage Replica in Windows Server 2016 is to build a stretched file server cluster based on two VMs on two different sites. With this configuration you can build a highly available file server across two sites without the need of replicated SAN or similarly. Instead you can simply use the Storage which is locally available at each site and leverage Storage Replica to replicate the data volumes inside the VMs. In case one of the Sites fails, the File Server Role will automatically fail over to the second site and the end user will probably not even notice it.

![Stretched SR Cluster](/assets/images/SR_StretchedFileCluster.png){: .align-center}

Recently I have made some tests with such a set up in my Homelab where I had the need to rebuild quickly the whole environment. Therefore I made a simple script with all the needed PowerShell commands.

You can get a copy of the Script at my [GitHub Repository](https://github.com/J0F3/PowerShell/blob/master/StorageReplica/New-StretchedFileCluster.ps1)

The Script is intended to run on a third machine, like for example a Management Server which has the Windows Server 2016 RSAT Tools installed. Especially the Hyper-V, Failover Cluster and Storage Replica Cmdlets are required.

After you set the correct parameter values and you are really sure everything is right 😉, you can run the script in one step. Or, probably the more educational approach, is to open the script in the PowerShell ISE and run the individual steps one by one.
  
For this purpose the script has comments which mark the individual steps:  
![SRClusterScript](/assets/images/SRFSClusterScript-1.png)

So have fun with PowerShell and Storage Replica. A very nice combination! 🙂