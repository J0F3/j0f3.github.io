---
title: 'SCVMM: When the deployment of new VM template suddenly fails'
date: 2017-03-02T08:55:37+00:00
dsq_thread_id:
  - "5596359840"
classes: wide
header:
  teaser: /assets/images/unattend-error-VM.png
tags:
  - Hyper-V
  - Microsoft System Center
  - SCVMM
  - Windows Server
  - Windows Server 2016
---

Recently I ran in a very strange behavior when deploying a VM template with Server 2016 through VMM 2012 R2. First of all, to enable the full support of Windows Server 2016-based VMs in VMM 2012, you need at least Update Rollup 11 Hotfix 1 installed. But even after installing the latest UR (UR12 in my case), the deployment of a Server 2016 VM had failed.

## The Issue

Every time when a new VM is deployed from a Server 2016 VM template the process fails at specialize phase of the sysprep. However all other existing templates with Server 2012 were working as expected.

![VMMSysprepError](/assets/images/unattend-error-VM.png)

Because in in this phase also the domain join happens I decided to give another try with a VM template which has no domain join configured. And tada, the VM was deployed successfully.

## The root cause
With this finding my assumption was that, when the VM template is configure for domain join, VMM adds something in the unattend.xml which Server 2016 does not like that much. So I inspected the unattend.xml file of a failed deployment and there I found the following section which has looked a litte bit strange:

![unattentwitherror](/assets/images/unattend_notworking.png)

So, somehow the Domain of the domain join account was missing.

## The Solution
  
I checked then the VMM Run As Account which was specified as domain join credentials in the VM template. And as you can see, we have also no domain information here.

![vmmrunasaccount](/assets/images/runasaccount.png)

After changing the username to &#8220;domain\vm domain join&#8221; the deployment went through smooth as it should. Inspecting the unattend.xml file showed that the domain is now also correctly filled in.

![unattendok](/assets/images/unattend_working.png)

## Conslusion
  
When the deployment of a new VM Template in VMM suddenly fails at the domain join step, double check the run as account and be sure that there is also the domain name in the username field. In my case it was a template with Server 2016. But I think chances are good as the same could also happens with new VM templates with another guest OS.