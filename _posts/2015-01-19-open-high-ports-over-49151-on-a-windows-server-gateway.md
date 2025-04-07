---
title: Open high ports (over 49151) on a Windows Server Gateway
date: 2015-01-19T19:16:50+00:00
dsq_thread_id:
  - "4741447865"
classes: wide
header:
  teaser: /assets/images/WAPNATHighPort.png
tags:
  - HNV
  - Hyper-V
  - NVGRE
  - SCVMM
  - Windows Azure Pack
  - Windows Server
  - Windows Server 2012 R2
---

In a cloud infrastructure with System Center Virtual Machine Manager (SCVMM), Hyper-V and Azure Pack the Windows Server Gateway could provide the tenants with the possibility to connect their virtual network, provided by Hyper-V Network Virtualization (HNV, NVGRE), with the Internet via NAT. The tenant has then also the possibility to open and forward inbound ports to his VMs. For example he can open Port 80 to run a Webserver which is public reachable over the internet.

Basically this works very well. But lately I had a situation where I had to forward TCP Port 60000. So I was going the Azure Pack Tenant Portal and was trying to add a new Network Rule like I did it several times before: ![WAPNATHighPort](/assets/images/WAPNATHighPort.png)

But then it happens. The operation failed with a strange error: ![WAPErrorHighPort](/assets/images/WAPErrorHighPorts.png)

Then I had a look in SCVMM and found this, not very instructive, error: ![VMMErrorHighPort](/assets/images/VMMErrorHighPort.png)

So I digged in a little deeper and discovered on the gateway VM that SCVMM adds the external address for the tenants with the port range 1-49151. So that does explains why you can not forward Port over 49152 on multi tenant Windows Server NAT Gateway: ![Get-NetNatExternalAddress](/assets/images/Get-NetNatExternalAddress.png)

Probably the SCVMM defines this port range for the external address because all ports above 49151 where per [RFC6335](https://tools.ietf.org/html/rfc6335#section-6){:target="_blank"} actually destined for dynamic ports or private ports. In Windows the this range is also specified for the dynamic client port range for outgoing connections. (RPC Communication and so forth)

## Bonus, Possible solutions

### Option 1, manual intervention with PowerShell

But the RRAS Role in Windows which is also used for multi tenant NAT Gateway has no restriction which would hinder you define external address with the whole port range from 1-65535 with PowerShell. In fact when you set an external address with PowerShell the default values for PortStart and PortEnd is 1024 and 65535.

This means you can remove the external address set by scvmm and add the address again with PowerShell with the whole port range. This can achieved by the following steps:

* Get all external IP Address with "Get-NetNatExternalAddress" and note the values form the parameter "NatName" and "IPAddress" from the definition which you want to change.
* Remove the existing external Address definition: ![Get-NetNatExternalAddress_example](/assets/images/Get-NetNatExternalAddress_example.png)

  ``` powershell
  Get-NetNatExternalAddress -NatName [NATName] | Remove-NetNatExternalAddress
  ```

* Add a New external Address with the same value for NatName and IPAddress but with the new port range: ![Add-NetNatExternalAddress](/assets/images/Add-NetNatExternalAddress.png)

  ``` powershell
  Add-NetNatExternalAddress -NatName [NATName] -IPAddress [ExternalIPAddress] -PortStart 1 -PortEnd 65535
  ```

Afterward you can head again to the tenant portal and now you can add a Network Rule with a port greater that 49151 without any problem. 😁

### Option 2, Registry setting for SCVMM

After some further research I found that SCVMM has a undocumented Registry setting where you can specify the end port for the external address definition on the NAT Gateway. By creating the following Registry Key SCVMM configures automatically the whole port range (1 to 65535) if a tenant activates the NAT Gateway for his VNet in the Tenant Portal.

![VMMNatPortRange](/assets/images/VMMNatPortRange.png)

HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Microsoft System Center Virtual Machine Manager Server\Settings\NATPortRangeEnd, REG_DWORD, Value: 65535

>**Disclaimer:**  
Use these settings at your own risk! These where NO official solutions from Microsoft and changing these settings will lead probably to a unsupported situation! So be careful! 😉
{: .notice--danger}