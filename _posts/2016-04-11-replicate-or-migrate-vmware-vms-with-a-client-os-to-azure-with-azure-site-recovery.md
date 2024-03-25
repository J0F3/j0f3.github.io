---
title: Replicate or migrate VMware VMs with a client OS to Azure with Azure Site Recovery
date: 2016-04-11T17:32:06+00:00
dsq_thread_id:
  - "4738715343"
classes: wide
header:
  teaser: /assets/images/ASRAgentError.jpg
tags:
  - ASR
  - Azure Site Recovery
  - Microsoft Azure
  - VMware
---

# The official (not working) way

To replicate VMware VMs to Azure you have to install the ASR Mobility Service in the VM. But what is when in the VM is running a client OS (Windows 7, 8.1, 10) instead of Windows Server? Officially this is not supported by Azure Site Recovery and when you try to install the Mobility Service you get the following nice, or not so nice 😉, message:

![ASRAgentError](/assets/images/ASRAgentError.jpg)

# The unofficial but working way

However, beside the fact that a single VM in Azure does not qualify for a SLA guarantee and may have down times, there is technically actually no reason why you cannot run a Client OS in an Azure VM. Especially if the VMs are used for Dev/Test scenarios. So why should it then not be possible to replicate or migrate these VMs to Azure with ASR, you may ask?  And you know what? With a little trick (installing the MSI directly on command line) it’s actually really possible. Here are the steps needed to get the Mobility Service running on a Client OS:

1. Get the Mobility Service .exe file from your ASR Process Server and copy it to temporary location on the VM which you want to replicate to Azure. You can find the setup file in the install folder of the Process Server under home\svsystems\pushinstallsvc\repository (e.g. D:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository\Microsoft-ASR\_UA\_9.0.0.0\_Windows\_GA\_31Dec2015\_Release.exe)

2. Run the exe and make notice of the folder to where the files get extracted by the installer  
![ASRAgentExtract](/assets/images/ASRAgentExtract.jpg)

3. Keep the Setup Wizard open and copy the content of the folder from step 2 to a temporary location

4. Now you can install the Mobility Service MSI directly with msiexec by executing the following command line.

   ```console
   msiexec.exe /i "C:\Temp\ASRAgent\UnifiedAgentMSI.msi" /qn ROLE="Agent" INSTALLDIR="C:\Program Files (x86)\Microsoft Azure Site Recovery\agent" CSIP="&lt;Process Server IP Address&gt;" CSPORT="443" /L+*V "C:\temp\UnifiedAgentMSIInstall.log"
   ```

5. Finally start "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\hostconfigwxcommon.exe" and enter the Passphrase of the ASR Process Server to connect the Agent to the ASR Server.  
![ASRAgentConfig](/assets/images/ASRAgentConfig.jpg)

That is it. Now you can replicate and failover the VM with ASR like any other Windows Server VM. Success! 😁