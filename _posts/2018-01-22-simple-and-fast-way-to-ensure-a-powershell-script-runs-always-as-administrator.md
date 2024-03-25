---
title: 'Simple and fast way to ensure a PowerShell script runs always as Administrator'
date: 2018-01-22T21:31:47+00:00
classes: wide
header:
  teaser: /assets/images/PowerShell.jpg
tags:
  - Automation
  - PowerShell
---

Sooner or later when you are writing PowerShell scripts you have the situation where you want to ensure that the Script is running with elevated user rights (aka "run as Administrator"). Often this is the case when the script should make some configurations changes or some Cmdlets, used in the script, works only with elevated user rights.

When you search the web you can find several solutions with functions or if statement to check the right of the user under which the script currently run and then abort if he does not have admin rights. But actually there is a simple, builtin, way to ensure the the script runs only in a PowerShell session which was started with "run as Administrator" Simply add the following line (with the #) at the top of your script:

```powershell
#Requires -RunAsAdministrator
```

When the Script is then started in a normal (not elevated) PowerShell session it fails with the following, very clear, error message:

>The script 'youscriptname.ps1' cannot be run because it contains a '#requires' statement for running as Administrator. The current Windows PowerShell session is not running as Administrator. Start Windows PowerShell by using the Run as Administrator option, and then try running the script again.

This works with PowerShell 4.0 and later and there also other 'Requires' statements which can be used in scripts. For example to ensure a specific version of a PowerShell Module is installed.  
A full reference abtout the statement can be found in the online [PowerShell Documentation](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_requires?view=powershell-5.1).