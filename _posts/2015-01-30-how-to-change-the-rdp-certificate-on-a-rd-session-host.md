---
title: How to change the RDP certificate on a RD Session Host
date: 2015-01-30T09:59:56+00:00
dsq_thread_id:
  - "4738732590"
tags:
  - RDS
  - Remote Desktop Services
  - Windows Server
  - Windows Server 2012 R2
---

In Windows Server 2012 R2 RD Deployment you will install a certificate for the RD Connection Broker, RD Web Access and RD Gateway in the Deployment Properties using Server Manager. But this does not change the certificate on sessions hosts in the RD Deployment and you will still get certificate warnings when connection to the Session Hosts.

To change the certificate on the Session Hosts manually do the following:

1. Install the Certificate and the Private Key in the computer certificate store.
2. Set the thumbprint of the installed certificate with PowerShell and WMI:

   ``` powershell
   Set-WmiInstance -Path ((Get-WmiObject -class "Win32_TSGeneralSetting" -Namespace root\cimv2\terminalservices -Filter "TerminalName='RDP-tcp'").__path) -argument @{SSLCertificateSHA1Hash="THUMBPRINT"}
   ```

The process ist also documented in detail at:  
[http://blogs.technet.com/b/askperf/archive/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2.aspx](http://blogs.technet.com/b/askperf/archive/2014/05/28/listener-certificate-configurations-in-windows-server-2012-2012-r2.aspx){:target="_blank"}