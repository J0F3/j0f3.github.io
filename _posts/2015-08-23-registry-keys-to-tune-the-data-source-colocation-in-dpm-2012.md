---
title: Registry keys to tune the data source colocation in DPM 2012
date: 2015-08-23T16:28:20+00:00
dsq_thread_id:
  - "4739917384"
tags:
  - DPM
  - Hyper-V
  - Microsoft System Center
---

By default, DPM will create for every data source two volumes (a replica and a shadow copy volume). For Hyper-V and SQL Database DPM can colocation multiple data sources on a single replica an shadow copy volume. This is relatively well known setting.The option is especially useful for backup a large numbers of Hyper-V VMs.

What is less know, is the possibility to tune the initial size of the replica volume which DPM will choose when a new Protection Group with colocation is created. The default size for Hyper-V VMs is 250 GB what is relatively small if youbacking up a lot of VMs. To change the initial size of the replica volume you can edit the following Registry setting:

```registry
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Collocation\HyperV\CollocatedReplicaSize (DWORD)
```

The value is the size in bytes and should be a multiple of 1073741824 (1GB).

The default value is: 268435456000 (250GB)
So when your backup a lot of VMs you cloud change the value e.g. to 900GB (966367641600) to fit a lot more VMs on the same volume than it would be the case with the default 250GB volume.

There is also a second Key to tune how many VM Backups DPM will colocation on the same volume:

```registry
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Collocation\HyperV\DSCollocationFactor (DWORD)
```
Values between 1 and 8 are possible. MS recommends a value of 3 for large scale environments.

For more details, see: [https://blogs.technet.com/b/dpm/archive/2013/07/09/dpm-how-to-plan-for-protecting-vms-in-private-cloud-deployments.aspx](https://blogs.technet.com/b/dpm/archive/2013/07/09/dpm-how-to-plan-for-protecting-vms-in-private-cloud-deployments.aspx) and [https://technet.microsoft.com/en-us/library/jj628128.aspx](https://technet.microsoft.com/en-us/library/jj628128.aspx)
