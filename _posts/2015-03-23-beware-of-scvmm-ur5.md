---
title: Beware of SCVMM UR5 if you have Hyper-V Clusters with Storage Spaces
date: 2015-03-23T12:18:01+00:00
dsq_thread_id:
  - "4751758646"
classes: wide
tags:
  - Hyper-V
  - SCVMM
  - Windows Server
  - Windows Server 2012 R2
---

The latest Update Rollup (UR5) for SCVMM 2012 R2 seems to have some issues with Live Migration in environments where the Hyper-V hosts have Cluster Shared Volumes (CSV) directly stored on clustered Storage Spaces. So basically this is the case if you are using one of the following configuration for your Hyper-V clusters:

* Two Hyper-V host directly connected to a SAS JBOD.
* Cluster in a Box Systems like these from DataON.

> **Note:** The here described issue does not occur if the VMs are stored on a Scale-Out File Server or on a traditional Fiber Channel SAN
{: .notice--info}

## The Error

Anyway the issue I have noticed in UR5 with one of the above listed configuration, is that when you try to live migrate a VM in SCVMM the Live Migration fails with the following Error:

![Error20404](/assets/images/Error20404.png)

This only happens in SCVMM. Live Migration through Failover Cluster Manager still works fine.

After some troubleshooting I found also that the Live Migration works sometimes from e.g. Host 1 to Host 2 but not the other way. So this brings me to the conclusion that it must has something to do which host is the owner node of the CSV Volume. And some further tests has confirmed my assumption. If you change the owner node of a CSV which is stored on clustered Storage Spaces the whole disk is moved from on node to the other and this seems to confuse SCVMM.

As a result SCVMM inserts an invalid value (00000000-0000-0000-0000-000000000000) for "HostDiskID", for one of the hosts, in the "tbl\_ADHC\_HostDis" table in the SCVMM DB.

![tbl_ADHC_HostDisk](/assets/images/tbl_ADHC_HostDisk.png)

During the Live Migration pre checks, SCVMM runs a query to find a disk with the ID "00000000-0000-0000-0000-000000000000" in the DB which obviously does not exists. So the pre checks and Live Migration Job fails immediately with error 20404

## Solution / Conclusion:

> **Update April 29, 2015:** [UR6 for SCMM 2012 R2 ](https://support.microsoft.com/de-de/kb/3050317) is now released and includes a fix for this issue. Yeah! 😊
{: .notice--info}

After opening a Microsoft Support case and posting the issue [on connect.microsot.com](https://connect.microsoft.com/WindowsServer/feedback/details/1145435/hyper-v-cluster-live-migration-does-not-work-anymore-after-scvmm-2012-r2-ur5){:target="_blank"} I got the confirmation from Microsoft that this behavior is a bug in UR5. It will probably fixed in UR6 which is expect for April.

So my advice for everyone, which is using one of the above configuration together with SCVMM, is to stay on UR4 and wait for UR6. If it is not already too late. 😉
