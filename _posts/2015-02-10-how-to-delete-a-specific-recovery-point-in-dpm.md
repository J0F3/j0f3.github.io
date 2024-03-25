---
title: How to delete a specific Recovery Point in DPM
date: 2015-02-10T10:25:50+00:00
dsq_thread_id:
  - "4739168002"
tags:
  - DPM
  - PowerShell
---
There is no possibility to delete a recovery point (backup) in DPM via GUI. But good old friend PowerShell helps. 🙂

Here is an example. We want to delete the Recovery Point from the evening of February 8 from the Backup of the file server FSSRV001 in the Protection Group "File Backup". DPM Server name is "DPMSRV001".

1. First get the Protection Group to which the recovery point belongs

   ``` powershell
   $pg = Get-ProtectionGroup -DPMServerName DPMSRV001 | where {$_.Name -eq "File Backup"}
   ```

2. Then you need the Data Source from where the data in the recovery point comes from.

   ``` powershell
   $ds = Get-Datasource -ProtectionGroup $pg | where {$_.Computer -eq "FSSRV001"}
   ```

3. And finally you need to select the Recovery Point which you want to delete. You can identify the Recovery Point by Date and Time.

   ``` powershell
   $rp = Get-RecoveryPoint -Datasource $ds | where {$_.BackupTime -eq (get-date "08.02.2015 18:00:00")}
   ```

4. Now we have all of the needed information an we can delete the Recovery Point.

   ``` powershell
   Remove-RecoveryPoint -RecoveryPoint $rp
   ```
