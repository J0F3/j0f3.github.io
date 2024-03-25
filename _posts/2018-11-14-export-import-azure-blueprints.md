---
title: Export and import Azure Blueprints definitions
classes: wide
header:
  teaser: /assets/images/azblueprints.png
tags:
  - Microsoft Azure
  - PowerShell
---

Azure Blueprints are relatively new and currently in public preview. With Azure Blueprint you can deploy and update a set of different Azure resources and configurations in a repeatable way and can be really useful to define and apply your standards and foundations to Azure subscriptions.
For a general overview what Azure Blueprints are and how they can be used I would recommend to read the ['Azure Blueprints – Overview'](https://marcelzehner.ch/2018/10/04/azure-blueprints-overview/){:target="_blank"} blog post from Marcel Zehner.

After you made some blueprint definitions you may have the need to export them as a backup or to duplicate a Blueprints definition by exporting and importing it under a new name. However through the Azure portal that is currently not possible. But [Microsoft has published a PowerShell Script](https://www.powershellgallery.com/packages/Manage-AzureRMBlueprint){:target="_blank"} which can help you with that task.

The easiest way to use that script is in the Azure Cloud Shell. This way you basically only need access ot the Azure Portal any you are ready to use the script, regardless if the Azure PowerShell Module is installed on the computer you currently on.

## Preparation

First start a Azure Cloud Shell instance with PowerShell and install the latest version fo the script form the PowerShell gallery.

```powershell
Install-Script -Name Manage-AzureRMBlueprint
```

![Install-Script Manage-AzureRMBlueprint](/assets/images/InstallManage-AzureRMBlueprint.png)

## Export

To export a specific Blueprints definition run Manage-AzureRMBlueprint.ps1 in the "export" mode.

```powershell
Manage-AzureRMBlueprint.ps1 -mode export -ManagementGroupID [YourManagementGroup] -BlueprintName [YourBlueprintDefinitionName] -ExportDir $HOME
```
![exportBP](/assets/images/exportazbp.png)

If you like to save the export locally simply create a .Zip file out of the export and download it via the "Download/Upload" functionality of the Cloud Shell as follow.

```powershell
Compress-Archive -Path "$home/jonas/MG-[YourManagementGroup]" -DestinationPath "$home/bp-export.zip"
```

![download](/assets/images/dlcloudshell.png) ![download](/assets/images/dlcloudshell2.png) 

## Import

For importing a Blueprints definition run the script in the "import" mode and specify a new blueprint name.s

```powershell
Manage-AzureRMBlueprint.ps1 -mode Import -ImportDir "$home/jonas/[YourExportDir]" -ManagementGroupID '[YourManagementGroup]' -NewBluePrintName '[YourBPName]'
```

![importBP](/assets/images/importazbp.png)

> **Note:** If you specify a name of an existing Blueprints definition any artifacts removed from the definition since the export was made will be re-added again. However any new added artifact's will not be touched by the import.
{: .notice--info}
> **Caution:** Be ware that the script (currently?) does not fully handle the export of artifacts used in the Blueprints definition! Which means it will export only the references to the resources used as artifacts but not the resource itself (e.g. a custom Azure Policy definition). So for example if you use some custom polices in the Blueprint you will not be able to import the Blueprint to an other Management Group tree (under an other Azure AD directory) where the custom policy does not exists.  
Therefore the script is really more to backup and restore/duplicate Blueprints definition than to transfer the Blueprints definitions to other tenants / subscriptions under other Azure AD directories!
{: .notice--danger}

## Report

The script has also a "report" mode whit which you can create a CSV report of the Blueprints definition.

```powershell
Manage-AzureRMBlueprint.ps1 -Mode Report -ManagementGroupID '[YourManagementGroup]' -ReportDir $home
```

![report](/assets/images/reportazbp.png)

The report CSV file will then look like the following:  
![CSVreport](/assets/images/reportazbpcsv.png)

## Bonus Tip - Resolve 401 error

I got sometimes a 401 error because the access token was somehow expired in the Cloud Shell. (There are also some issues on GitHub related to similar issues in the Cloud Shell).

![401error](/assets/images/cloudshell401.png)

The message of the script is a bit misleading. ```Logout-AzureRMAccount``` did not help in my case. But ```Clear-AzureRmContext``` did the trick for me. After that you have however to authenticate again but afterward the script should run without authentication issues. So when you also hit the 401 error just run ```Clear-AzureRmContext``` and try again.