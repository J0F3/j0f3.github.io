---
title: Build a Windows Server 2016 TP3 VHD with minimal GUI
date: 2015-09-06T18:36:25+00:00
dsq_thread_id:
  - "4744262006"
tags:
  - PowerShell
  - Windows Server
  - Windows Server 2016
---

>**Update, 11/20/2015:**  
This does not work any more with the TP4 which is now public available as the feature "Server-Gui-Mgmt-Infra" is gone now. You can add the feature "Server-Gui-Mgmt" with DISM which gives you a similar experience. But the feature is not even listed in PowerShell (Get-WindowsFeature) so I think this is probably far form supported. With other words: No "Minimal Server Interface" in TP4 anymore.
{: .notice--danger}

In the TP3 the installation option was changed. Therefore, when you create a VHD(X) directly from the ISO with the Convert-WindowsImage.ps1 Script you have choice to create a VHD with Core Server or the full GUI with Desktop Experience but nothing in between. To create a VHD with the minimal server interface (core server with Server Manger and mmc GUIs) or the Server Graphical Shell (without Desktop Experience) you have to add the corresponding features with DISM.

This is how you add the minimal server interface to a VHD with the core server installation:

1. Create a Core Server VHD with the [Convert-WindowsImage.ps1](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f){:traget="_blank"} Script.
```powershell
Convert-WindowsImage -SourcePath .\en_windows_server_2016_technical_preview_3_x64_dvd_6942082.iso -VHDPath .\WS2016TP3.vhdx -Edition ServerDatacenterCore -SizeBytes 40GB
```
2. Mount the Windows Server 2016 TP3 ISO (double click it)
3. Mount the install.wim from the ISO with DISM  
(Index 4 for Datacenter, 2 for Standard Edition)
```powershell
dism /mount-wim /wimfile:E:\sources\install.wim /index:4 /mountdir:D:\mountdir /readonly
```
4. Add the Server-Gui-Mgmt-Infra (for Minimal Server Interface) or the Server-Gui-Shell (for full Graphical Shell) feature to the VHD by specify the mounted WIM as source.
```powershell
Install-WindowsFeature -Name Server-Gui-Mgmt-Infra -Vhd D:\WS2016TP3.vhdx -Source "D:\mountdir\Windows\WinSxS"
```