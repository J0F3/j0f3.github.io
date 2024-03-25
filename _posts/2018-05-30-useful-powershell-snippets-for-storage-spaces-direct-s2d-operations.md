---
title: Useful PowerShell snippets for Storage Spaces Direct (S2D) operations
classes: wide
header:
  teaser: /assets/images/hyper-converged-minimal.png
tags:
  - Automation
  - Hyper-V
  - S2D
  - Storage Spaces Direct
  - Windows Server
  - Windows Server 2016
---

This is a very quirk post to just share some simple PowerShell snippets I have created or found elsewhere which I find very handy when it comes to troubleshooting, setup or operating Storage Spaces Direct (S2D).

### 1. Get the count of physical disk in each node

When S2D is enabled in a cluster Get-Physicaldisk returns the physical disk from all cluster nodes. But when you want the disk count of each node the following script can be quite useful:

``` powershell
icm S2D-Cluster1 {
 $Drives = Get-StoragePool S2D* | Get-PhysicalDisk
 $Result = @()
 ForEach ($Drive in $Drives) {
    $NodeObj = $Drive | Get-StorageNode -PhysicallyConnected
    If ($NodeObj -Ne $Null) {
        $Node = $NodeObj.Name
    }
    Else {
        $Node = "-"
    }

    If ($Drive.BusType -Eq "NVMe") {
        $SerialNumber = $Drive.AdapterSerialNumber
        $Type = $Drive.BusType
    }
    Else{
        $SerialNumber = $Drive.SerialNumber
        $Type = $Drive.MediaType
    }

   $Result += [PSCustomObject]@{
        "FriendlyName" = $Drive.FriendlyName
        "SerialNumber" = $SerialNumber
        "HealthStatus" = $Drive.HealthStatus
        "Node" = $Node
    }
  }

  $Result

} | Group -NoElement -Property Node | Sort -Property Node,Count
```

### 2. Get the processed and completed GBytes of running Storage Jobs (e.g. Rebuild Jobs)

This one comes from the Jaromir Kaspar and his ws2016lab Project on GitHub, which is btw a very nice project to build a test lab in a highly automated way. So all credit goes to him for this script.  
It shows you the amount of GBytes the running storage jobs have already processed and how much must still to be processed until the jobs are finished.

``` powershell
$ClusterName="S2D-Cluster1"
$jobs=(Get-StorageSubSystem -CimSession $ClusterName -FriendlyName Clus* | Get-StorageJob -CimSession $ClusterName)
if ($jobs){
    do{
        $jobs=(Get-StorageSubSystem -CimSession $ClusterName -FriendlyName Clus* | Get-StorageJob -CimSession $ClusterName)
        $count=($jobs | Measure-Object).count
        $BytesTotal=($jobs | Measure-Object BytesTotal -Sum).Sum
        $BytesProcessed=($jobs | Measure-Object BytesProcessed -Sum).Sum
        [System.Console]::Write("$count Storage Job(s) Running. GBytes Processed: $($BytesProcessed/1GB) GBytes Total: $($BytesTotal/1GB)               `r")
        Start-Sleep 10
    }until($jobs -eq $null)
}
```

(original source: [ws2016lab project](https://github.com/Microsoft/ws2016lab))