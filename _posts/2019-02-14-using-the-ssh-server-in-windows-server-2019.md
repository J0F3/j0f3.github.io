---
title: Using the SSH server in Windows Server 2019
classes: wide
excerpt: "Yes, your are reading it right. Windows Server has now also a built in SSH Server for remote access. And it is not just any SSH server it is the popular OpenSSH server."
header:
  teaser: /assets/images/opensshwin.png
date: 2019-02-14
tags:
  - Windows Server
  - Windows Server 2019
  - OpenSource
  - SSH
  - Cross-platform
---

![OpenSSHWindows](/assets/images/opensshlogo.png) ![WindowsServer](/assets/images/windows-server-2019-logo.png)

Yes, your are reading it right. Windows Server has now also a built in SSH Server for remote access. And it is not just any SSH server it is an official Win32 port of the popular OpenSSH server, maintained and [official supported by Microsoft](https://blogs.windows.com/buildingapps/2018/12/11/windows-server-2019-includes-openssh/?utm_campaign=blogfy19&utm_source=Twitter&utm_medium=Social&utm_content=windows_server_2019_includes_openssh){:target="_blank"}. The same OpenSSH you already may know form your favorite Linux distribution or from macOS. So an other nice example how Microsoft has changed in the last years and got open minded to the OpenSource world.

## Why SSH server on Windows?

First of all you may ask you now for what we need an SSH server in Windows server at all. In a Windows only environment SSH may appear really not that useful. After all we have RDP and PowerShell Remoting with WinRM available which provides already a strong remote management capability. Never less, especially in a heterogeneous environment, SSH on Windows has definitely it uses and advantages. I think on things like:

- Easy connect to and manage Windows (core) servers from a Linux or MacOS using the built in tools.
- Connecting form Windows systems to Linux server is a straight forward experience with the integrated SSH client.
  I think here also to all the Linux admins out there which have to manage the servers at work from a Windows Client and always have to install some additional tools like PuTTY or WinSCP first. That is now over, now they can use the familiar ssh commands directly from the command line on  Windows. Is that not nice? 😊
- Use the same remote management tools for Linux and Windows servers (ssh, scp, public-key authentication, etc.)
- Cross-platform PowerShell Remoting. PowerShell Core uses SSH beneath to enable remote PowerShell session across Windows, MacOS and Linux. Where in contrast the WinRM based PowerShell Remoting of Windows PowerShell only works on Windows.  
  See [PowerShell Remoting Over SSH](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6){:target="_blank"} on docs.microsoft.com for more details.
- Together with the Windows Subsystem for Linux you can get Linux like SSH sessions with Bash and common Linux tools also on a Windows server which lets Linux admins use their knowledge also for managing Windows systems.
- Ore wise versa. Windows admins can use the known PowerShell to mange Linux server using PowerShell Remoting to Linux servers.
- Or just an other option more to remote manage a Windows server which gives us even more flexibility. From which you never can have enough in the current fast and ever changing cloud world.

So lets see it in action now! 🙂

## Installing OpenSSH

The OpenSSH server ans client are provided as an optional Windows Feature for Windows 10 1809 (and later) and Windows Server 2019. The server can be installed by launching PowerShell as an Administrator and running the following command:

```powershell
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

> **Note:** The OpenSSH client is already installed by default (since Windows 10 1809), so Windows has now finally a built-in ssh and scp functionality! 😎
{: .notice--info}

## Initial configuration

After installing the SSH Server feature simply start the sshd server and make sure that the Windows Firewall rules for the SSH server are enabled. Optional also set the sshd service to automatically start.

```powershell
# Start the SSH server service
Start-Service sshd

# Confirm the Firewall rule is enabled. It should be enabled automatically by default.
Get-NetFirewallRule -Name *ssh* | Enable-NetFirewallRule

# Optional: set the startup type of the sshd service to automatic.
Set-Service -Name sshd -StartupType 'Automatic'

```

## Connect with SSH to your Windows Server

Now we are basically ready to go and we can connect with a SSH client to our Windows Server. This can either be done from Windows 10, a Linux machine, with putty.exe on an older Windows machine, or even from Bash in Windows 10. All you need is some sort of SSH client, specify your username and your server name or ip address and connect.

For example:

```powershell
ssh jfeller@WindowsServer01
```

or

```powershell
ssh -l jfeller WindowsServer01
```

> **Note:** It may not really obvious but the username can also be a domain user if the Windows Server is domain joined and the user is permitted to login on to the server.
{: .notice--info}

Then you got a Windows Command shell from the remote machine provided through the SSH connection.  
![startsshconn](/assets/images/sshconnectwin.png)  

![sshonwin](/assets/images/sshonwin.png)

## Only CMD? Of course not!

![powershellthroughssh](/assets/images/powershellthroughssh.png)

You can use basically any shell you have on your windows machine also through the SSH connection. Even Bash is possible when the Windows Subsystem for Linux (WSL) is also installed on the target machine. But this my be a topic for an other post...
Changing the default shell of the SSH server to something other than the command shell is also possible. To do so the 'DefaultShell' Registry Key can be changed.

```powershell
New-ItemProperty -Path "HKLM:\SOFTWARE\OpenSSH" -Name DefaultShell -Value "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -PropertyType String -Force
```

## Copying files

Also like with an OpenSSH server on any *nix system you can use SCP to copy file to or from the server.  
So for example a Linux admin can quickly get a log file form a Windows server with the same command as he would use for a Linux server.  
![scpfromwin](/assets/images/scpfromwin.png)

> **Note:** One thing to remember when you connect form Bash/*nix to a windows machine is that the Windows paths must also be specified with the unix typical forward slashes instead of the back slashes. So C:/Windows instead of C:\Windows for example.
{: .notice--info}

## SSH public-key authentication

It is also possible to use key based authentication instead of the password authentication. Something that is quit popular in *nix world.
[Microsoft has a detailed guide](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement){:target="_blank"} how to setup the public-key authentication on the Windows SSH server.

## sshd_config

As on Linux the OpenSSH server has also on Windows a configuration file to do some more detailed settings. For example to restrict the SSH login to some specific users or groups.  
By default the configuration file is found under '%programdata%\ssh\sshd_config'.  
The possible configuration options are in general documented by OpenSSH: [https://man.openbsd.org/sshd_config](https://man.openbsd.org/sshd_config){:target="_blank"}

In additional Microsoft has also a documentation for [Windows specific settings](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_server_configuration){:target="_blank"}.

## More information

More information about OpenSSH in Windows can be found on [docs.microsoft.com](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_overview){:target="_blank"}
or in the [Github project](https://github.com/PowerShell/openssh-portable){:target="_blank"} of the OpenSSH fork from Microsoft.

So long and happy SSH-ing! 🐡
