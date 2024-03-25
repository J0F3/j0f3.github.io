---
title: PowerShell DSC resource to enable/disable Microsoft Update
date: 2015-06-16T14:46:10+00:00
dsq_thread_id:
  - "4783807788"
tags:
  - PowerShell
  - PowerShell DSC
  - Windows Server
  - Windows Server 2012 R2
---

![MSUpdate](/assets/images/MicrosoftUpdate.png)

Ever get tired to manually set the check box for Microsoft Update in the above screen on a bunch of servers (e.g. in a new test lab setup or so)? Then this one is for you. I wrote recently, mostly as an exercise, a PowerShell DSC Module with a resource to enable (or disable) the Microsoft Update.

I have then published the Module von GitHub to get another exercise. 😉 So if you interested you can get the Module from here: [https://github.com/J0F3/cMicrosoftUpdate](https://github.com/J0F3/cMicrosoftUpdate)

>**Note:** The cMicrosoftUpdate resource is integrated in the official Microsoft community resource [xWindowsUpdate](https://github.com/PowerShell/xWindowsUpdate) in the meantime
{: .notice--info}

After you get the module, enabling the Microsoft Update settings will look like this: ![DSCMSUpdate](/assets/images/EnableMSUpdateDSC.png)

Happy DSCing!😁