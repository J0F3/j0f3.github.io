---
title: How to Build a Source for Windows 10 Enterprise from the official Insider Preview ISO (Build 10162)
date: 2015-07-04T16:09:52+00:00
dsq_thread_id:
  - "4743296714"
tags:
  - Windows 10
---

The official Windows 10 Preview ISO from Microsoft installs only the Pro or Core Version. So it can not be used to install or upgrade the Enterprise Edition. However the Sources can be easily "upgraded" to the Enterprise Edition using DISM on a existing Windows 10 Installation:

1. Download the official ISO from the in your desired Language.
2. Mount the ISO file and copy the content to a new folder. e.g. C:\temp\W10_10162
3. Start a PowerShell as admin and mount the install.wim from the sources Folder:

   ``` powershell
   Mount-WindowsImage -Path C:\temp\mount\ -ImagePath C:\temp\W10_10162\sources\install.wim -Index 1
   ```

4. Change the edition of the install.wim file with the Set-WindowsEdition command:

   ``` powershell
   Set-WindowsEdition -Path C:\temp\mount\ -Edition Enterprise
   ```

5. After that dismount the Image:

   ``` powershell
   Dismount-WindowsImage -Path C:\temp\mount\ -Save
   ```

Now you can run directly the setup.exe in the C:\temp\W10_10162 to do an inplace upgrade of a older Windows 10 Enterprise Build. If you prefer a clean Installation, copy the files to a bootable USB Stick and reboot.

If you get asked for a product key, this one should work:[https://social.technet.microsoft.com/Forums/en-US/065a430e-a5c2-4536-b2d0-1c62134e4fa8/cant-activate-win-10-enterprise-after-upgrading-to-build-10162?forum=WinPreview2014Setup](https://social.technet.microsoft.com/Forums/en-US/065a430e-a5c2-4536-b2d0-1c62134e4fa8/cant-activate-win-10-enterprise-after-upgrading-to-build-10162?forum=WinPreview2014Setup){:target="_blank"}