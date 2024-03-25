---
title: Azure Backup the future (replacement) of DPM?
date: 2015-10-09T09:33:19+00:00
dsq_thread_id:
  - "4798860327"
tags:
  - DPM
  - Microsoft Azure
  - Microsoft System Center
gallery:
  - url: /assets/images/AZBackupSetupSplash.png
    image_path: /assets/images/AZBackupSetupSplash_s.png
    alt: "splashscreen"
  - url: /assets/images/AZBackupSetup1.png
    image_path: /assets/images/AZBackupSetup1_s.png
    alt: "SetupScreen1"
  - url: /assets/images/AZBackupSetup2.png
    image_path: /assets/images/AZBackupSetup2_s.png
    alt: "SetupScreen2"
  - url: /assets/images/AZBackupSetup3.png
    image_path: /assets/images/AZBackupSetup3_s.png
    alt: "SetupScreen3"
  - url: /assets/images/AZBackupSetup4.png
    image_path: /assets/images/AZBackupSetup4_s.png
    alt: "SetupScreen4"
  - url: /assets/images/AZBackupSetup5.png
    image_path: /assets/images/AZBackupSetup5_s.png
    alt: "SetupScreen5"
  - url: /assets/images/AZBackupSetup6.png
    image_path: /assets/images/AZBackupSetup6_s.png
    alt: "SetupScreen6"
  - url: /assets/images/AZBackupSetup7.png
    image_path: /assets/images/AZBackupSetup7_s.png
    alt: "SetupScreen7"
  - url: /assets/images/AZBackupSetup8.png
    image_path: /assets/images/AZBackupSetup8_s.png
    alt: "SetupScreen8"
  - url: /assets/images/AZBackupSetup9.png
    image_path: /assets/images/AZBackupSetup9_s.png
    alt: "SetupScreen9"
  - url: /assets/images/AZBackupSetup10.png
    image_path: /assets/images/AZBackupSetup10_s.png
    alt: "SetupScreen10"
  - url: /assets/images/AZBackupSetup11.png
    image_path: /assets/images/AZBackupSetup11_s.png
    alt: "SetupScreen11"
  - url: /assets/images/AZBackupSetup12.png
    image_path: /assets/images/AZBackupSetup12_s.png
    alt: "SetupScreen12"
  - url: /assets/images/AZBackupSetup13.png
    image_path: /assets/images/AZBackupSetup13_s.png
    alt: "SetupScreen13"
  - url: /assets/images/AZBackupSetup14.png
    image_path: /assets/images/AZBackupSetup14_s.png
    alt: "SetupScreen14"
  - url: /assets/images/AZBackupSetup15.png
    image_path: /assets/images/AZBackupSetup15_s.png
    alt: "SetupScreen15"
  - url: /assets/images/AZBackupSetup16.png
    image_path: /assets/images/AZBackupSetup16_s.png
    alt: "SetupScreen16"
  - url: /assets/images/AZBackupSetup17.png
    image_path: /assets/images/AZBackupSetup17_s.png
    alt: "SetupScreen17"
  - url: /assets/images/AZBackupSetup18.png
    image_path: /assets/images/AZBackupSetup18_s.png
    alt: "SetupScreen18"
  - url: /assets/images/AZBackupSetup19.png
    image_path: /assets/images/AZBackupSetup19_s.png
    alt: "SetupScreen19"
  - url: /assets/images/AZBackupSetup20.png
    image_path: /assets/images/AZBackupSetup20_s.png
    alt: "SetupScreen20"
---
As Aidan Finn (and probably many others) wrote on [his blog](http://www.aidanfinn.com/?p=19116){:traget="_blank"} Microsoft has published a new Version of the Azure Backup Software. The new Software has now the ability to Backup workloads such as Hyper-V VM, SQL-Server, SharePoint and Exchange on premise to disk (B2D) and backup to the Cloud for long term retention. All in all, it sounds very similar to a DPM installation with the Azure Backup Agent. So it seems that DPM has a reborn, apart from the System Center Suite, as Azure Backup. So I decided to do a test installation and here is a how it looks like:

Firs you need an Azure Subscription with Backup Vault. For my Test I create a new Vault:
![BackupVault](/assets/images/06-10-_2015_Vault.png)

Once the Backup Vault is created you can Download the new Azure Backup Setup:
![DownlaodLink](/assets/images/06-10-_2015_AZBackupDL.png)

In additional to the Azure Backup setup you must also download the Vault credentials which you need later in the setup:
![DownloadCred](/assets/images/AZBackupDLCred.png)

After the Download you need to extract the files and then start setup.exe. And then the Setup Wizard start. If you are familiar with DPM you will notice the remarkable resemblance. Note the link for DPM Protection Agent, DPM Remote Administration on the first Screen 😉
{% include gallery%}
Finally, after Setup you have a Server with Azure Backup. The Console looks still like a DPM clone. Expect that the ability for Backup to Tape is missing everything is very similar to the Management Console from DPM 2012 R2:
![Console](/assets/images/AZBackupConsole.png)

If MS will really use DPM as basis for the Azure Backup I am very curious to see how MS will tune the underlying DPM in the future to handle big data source like files servers with multiply TBs of Data which is not necessary abnormally these days. But that's where DPM has really big drawback at the moment. But we will see. 😊