---
-title: Configuring Hyper-V Hosts with converged networking through PowerShell DSC
date: 2015-12-10T23:22:59+00:00
-dsq_thread_id:
  - "4754701297"
tags:
  - Hyper-V
  - PowerShell
  - PowerShell DSC
  - Windows Server
---
Lately I had to rebuild the Hyper-V Hosts in my home lab several times because of the release of the different TPs for Windows Server 2016. This circumstance (and because I am a great fan of PowerShell DSC 😉 gave me the Idea to do the whole base configuration of the Hyper-V Host, including the LBFO NIC Teaming, vSwitch and vNICs for the converged networking configuration, through PowerShell DSC.
  
But soon I realized that the DSC Resource Kit from Microsoft provides DSC resources only for a subset of the needed configuration steps. The result was some PowerShell modules with my custom DSC resources.

**My custom DSC resources for the approach:**

![FindModulecLBFOTeam](/assets/images/FindModulecLBFOTeam.png)

* **cLBFOTeam:** To create and configure the Windows built-in NIC Teaming
* **cVNIC:** To create and configuring virtual network adapters for Hyper-V host management
* **cPowerPlan:** To set a desired Power Plan in Windows (e.g. High Performance Plan)

You can get the modules from the [PowerShell Gallery](https://www.powershellgallery.com/packages?q=j0f3) (Install-Module) or from [GitHub](https://github.com/J0F3?tab=repositories). They will hopefully help everyone who has a similar intend 😉

**More to come:**  
Yes, I am not quite finished yet and I have more in the pipeline.
Currently I am also working on a fork of the xHyperV Module with a adopted xVMSwitch resource with a parameter to specify the MinimumBandwidth mode of the Switch. Furthermore I am also planing to add support for the SET (Swicht Embedded Teaming) in Windows Server 2016 to the xVMSwitch resource.

So you may soon read more about this topic here. In the meantime, happy DSCing! 😊