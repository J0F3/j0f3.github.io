---
title: Be aware of DSC pull server compatibility issues with WMF 5.0 and 5.1
date: 2017-02-20T17:30:25+00:00
dsq_thread_id:
  - "5568950675"
classes: wide
header:
  teaser: /assets/images/DSCPullServerConnErr.jpg
tags:
  - PowerShell
  - PowerShell DSC
  - Windows Server
  - Windows Server 2012 R2
  - Windows Server 2016
---

Apparently, there are some incompatibilities when WMF 5.0 computers wants to communicate with a DSC pull server running on WMF 5.1 or vice versa. This is especially the case when the "client" node and the pull server are not running the same OS version. For example, when you have a DSC pull server running on Server 2012 R2 (with WMF 5.0) and some DSC nodes running on Server 2016 (which as WMF 5.1 built in).

Currently I experienced two issues:

1. A DSC pull client running on WMF 5.1 cannot send status reports when the DSC pull server is running still on WMF 5.0. This is because WMF 5.1 has invented the new "AdditinalData" parameter in the status report. I have reported this bug also on GitHub: <https://github.com/PowerShell/PowerShell/issues/2921>
2. A DSC pull client running von WMF 5.0 cannot communicate at all with a DSC pull server running on WMF 5.1. ![pullservererror](/assets/images/DSCPullServerConnErr.jpg)

# Solution / Workaround for issue 1

As the WMF 5.1 RTM is now (again) available the simplest solution would be to upgrade the server and/or client to WMF 5.1. However, when you have to upgrade the DSC pull server then you must create a new EDB file and re-register all clients. Otherwise the issue preserve because the "AdditionalData" field is still missing in the database.

# Solution / Workaround for issue 2

The root cause of this issue can be found in the release notes of WMF 5.1:

>"Previously, the DSC pull client only supported SSL3.0 and TLS1.0 over HTTPS connections. When forced to use more secure protocols, the pull client would stop functioning. In WMF 5.1, the DSC pull client no longer supports SSL 3.0 and adds support for the more secure TLS 1.1 and TLS 1.2 protocols."

So, starting with WMF 5.1 the DSC pull server does not support TLS 1.0 anymore, but in reverse a DSC pull client running on WMF 5.0 is still using TLS 1.0 and can therefore not connect anymore to the DSC pull server.

The solution, without deploying WMF 5.1 to all pull clients, is to alter the behavior of the DSC pull server so he accepts again TLS 1.0 connections. This can be done by changing the following registry key on the DSC pull server:

``` console
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server\Enabled
```

Change Value from 0x0 to 0x1 and reboot the DSC pull server.
  
Afterward DSC pull clients running on WMF 5.0 can connect again to the DSC pull server.