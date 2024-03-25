---
title: How to enable CredSSP for PowerShell Remoting through GPO
date: 2016-10-19T17:13:29+00:00
dsq_thread_id:
  - "5236012862"
tags:
  - GPO
  - PowerShell
  - Windows Server
  - Windows Server 2012 R2
  - Windows Server 2016
---

In a domain environment CredSSP can easily enabled through a GPO. To do so there are three GPO settings to configure:

1. Computer Configuration > Administrative Templates > Windows Components > Windows Remote Management (WinRM) > WinRM Client > **Allow CredSSP Authentication (Enable)** ![allowcredssp](/assets/images/allow-credssp.png)

2. Computer Configuration > Administrative Templates > Windows Components > Windows Remote Management (WinRM) >  WinRM Service > **Allow CredSSP Authentication (Enable)** ![allowcredssp](/assets/images/allow-credssp.png)

3. Computer Configuration > Administrative Templates  > System > Credential Delegation > **Allow delegation of fresh credentials (add wsman/*<.FQDN of your domain>)** ![allowfreshcred](/assets/images/allow-fresh-cred.png)

4. If in your environment are computers in an other, not trusted, AD domain to which you want connect using explicit credential and CredSSP you have to enabled also the following GPO setting.  
Computer Configuration > Administrative Templates  > System > Credential Delegation > **Allow delegation of fresh credentials with NTLM-only server authentication (add wsman/*<.FQDN of your other domain>)** ![allowfreshcred](/assets/images/allow-fresh-cred-ntlm.png)

Now you are ready to use CredSSP within your PowerShell remote sessions.

# And a final word of warning! 😉
  
When you are using CredSSP your credentials were transferred to the remote system and your account is then a potential target for a pass-to-hash attack. Or with other words an attacker can steal your credentials. So only use CreddSSP with your PowerShell Remote session if you really have a need for it!