---
title: Query Terminal Services Profile Path of AD Users through PowerShell
date: 2015-04-09T18:29:00+00:00
dsq_thread_id:
  - "4740249220"
tags:
  - PowerShell
  - Active Directory
---

If you like to query Terminal Services or Remote Desktop Server Profile Path with PowerShell you cannot use the Get-ADUser Cmdlet. Instead you have to go through ADSI. The Scripting Guy has explained this in detail on his blog: [https://blogs.technet.com/b/heyscriptingguy/archive/2008/10/23/how-can-i-edit-terminal-server-profiles-for-users-in-active-directory.aspx](https://blogs.technet.com/b/heyscriptingguy/archive/2008/10/23/how-can-i-edit-terminal-server-profiles-for-users-in-active-directory.aspx){:traget="_blank"}

This works basically very well for all user object where the path for the Terminal Services Profile is set or was set sometime in the past and is now empty. But if you have a user object for which the Terminal Services settings in AD were never touched you get a funky error message:
  
>Exception calling "InvokeGet" with "1" argument(s): "The directory property cannot be found in the cache."

If you do an ad hoc query then this is not really a problem. But if you want to export the settings for all ad users into a CSV file the error will probably bother you.

So what we can do? If you have a look at the properties of the ADUser object, which the Get-ADUser Cmdlet returns, you can see that there is a property with the name "userProperties" with a cryptic value. That's where the Terminal Services Profile Path is actually stored.

![userparamaduser](/assets/images/userparamaduser.png)

But it the User Object had never set a Terminal Service Profile Path this property does simply not exist:

![nouserparamaduser](/assets/images/nouserparamaduser.png)

Now, as workaround, you can first check for the existence of "userProperties" property before you query the Terminal Services Profile Path with ADSI. This could look like this:

``` powershell
$ADUsers = Get-ADUser -Filter $Filter -Properties *
      foreach ($UserObject in $ADUsers){
        If($UserObject.userParameters -eq $Null){
            $tsprops = @{
                "TerminalServicesallowLogon" = "1"
                "TerminalServicesHomeDirectory" = ""
                "TerminalServicesHomeDrive" = ""
                "TerminalServicesProfilePath" = ""
            }
        }
        Else
        {
            $ADSIObject = [adsi]"LDAP://$($UserObject.DistinguishedName)"

            $tsprops = @{
                "TerminalServicesallowLogon" = $ADSIObject.PSBase.invokeget("allowLogon")
                "TerminalServicesHomeDirectory" = $ADSIObject.PSBase.invokeget("TerminalServicesHomeDirectory")
                "TerminalServicesHomeDrive" = $ADSIObject.PSBase.invokeget("TerminalServicesHomeDrive")
                "TerminalServicesProfilePath" = $ADSIObject.PSBase.invokeget("TerminalServicesProfilePath")
            }
        }
```