---
title: Azure Stack – The Azure in your data center
date: 2017-08-28T19:25:16+00:00
dsq_thread_id:
  - "6102607906"
classes: wide
header:
  teaser: /assets/images/intergratedsystems.jpg
tags:
  - Microsoft Azure
  - Microsoft Azure Stack
  - Windows Azure Pack
---

With the beginning of this year Microsoft Inspire conference (formerly Microsoft Worldwide Partner Conference – WPC) the long-awaited Microsoft Azure Stack became GA and is now order able from hardware vendors. But before you order your own Azure Stack instance it’s important to know what Azure Stack exactly is and if it makes even sense for you.

### The continued Cloud OS Vision

![one consistent platform](/assets/images/oneplatform.png "image source: Microsoft")

Long, long time ago 😉, about 4 years ago, together with the release of Windows Server and System Center 2012 R2, Microsoft came up with the vision to give the customers a platform which is consistent to Azure. The idea behind that is that regardless if your application is running in Azure, in your On-Premises data center or in the data center of a local Service Provider, always the same platform is underneath. But when we look back, with the 2012 R2 suit and the Windows Azure Pack as customer facing Self-Service portal, this goal was not really reached. In the meantime, even the consistent experience of the Self-Service portal is gone. Azure Pack was based on the “old” Azure Management Portal which is now, in public Azure, mostly replaced by the new ARM based Azure Portal.

### Azure Stack the successor of Windows Azure Pack?
Since the announcement of Azure Stack (which is now nearly two year ago by the way) there is ongoing some confusion in the IT world. For many Azure Stack seems to be the successor and replacement for System Center and Windows Azure Pack or simply anything was Microsoft has released for the data center before Azure Stack. But, this is not what Azure Stack is supposed to be. Even when the Cloud OS vision is clearly still recognizable in Azure Stack it is, however, a completely new product category which has Microsoft never done in this form before. Even more it is not an alternative for Azure or the replacement of traditional virtualization infrastructures (based on System Center and Hyper-V, VMware or whatever). Azure Stack is much more Azure or part of your Azure strategy. And therefore, you must commit to Azure when you want to use Azure Stack.

### The integrated system experience – Or the Azure Stack Appliance
![Integrated system](/assets/images/intergratedsystems.jpg "image source: Microsoft")  
So, what does "a new product category" mean? It is relatively simple. Azure Stack is not delivered as a software which you can setup on your own defined hardware and configure for your individual needs. Azure Stack will be basically delivered as an appliance which is specified, build and updated by the hardware vendor of your choice and Microsoft. Or in other words Azure Stack is a SAN equivalent system which provides not storage but Azure Services in your data center. This means, for you as a customer, that you have more time to focus on running applications, provide value-added services to your customers and develop modern cloud applications instead of keeping your virtualization infrastructure up & running.

**New IT roles for operating the "Appliance"**  
In Microsoft eyes to running an IT infrastructure in this new "Appliance" form, leads to two new roles in IT. The Cloud Architect and the Cloud Administrator or Operator.

The **Cloud Architect** is the one who does ensure that the Azure Stack "Appliance" can get properly integrated in the existing IT infrastructure (Network, Monitoring Systems, Identity System etc.). He does also plan the offering on the Azure Stack for internal or external customers. These are short-term tasks which can also perfectly done by an external partner.

After Azure Stack is integrated in your IT infrastructure the **Cloud Operator or Administrator** is responsible to operating the Azure Stack. But this is not a very high skilled role and probably also not a very time intensive task. Because of the appliance approach Azure Stack is operated by a simple management web interface (like the Azure Portal) and not by complicated Administrator consoles for which a deep knowledge of the whole system is necessary. The Cloud Operator will mainly monitor the integrated Azure Stack system and when a red light comes up he will either do simple remediate actions (e.g. restarting a service or apply an update) or he will contact the support which is provided jointly by Microsoft and the hardware vendors.

### Do I need Azure Stack?
![cloud as you need](/assets/images/cloudasyouneed.png "image source: Microsoft")

Azure Stack is and will not be the all mighty platform for everyone and every use case. Azure Stack is for you when you want to adopt the cloud model and develop and run modern cloud applications, which are depends at most partially on (IaaS) VMs. But because of various reasons you cannot go directly to Azure. Such reasons can for example be requirements for low latency, law and regulations which restricts to store data outside of a specific country or bad or no internet connectivity. For all other use cases there is still Hyper-V, System Center and Windows Azure Pack. They will be fully supported and maintained from Microsoft for, at least, the next 5 years. Windows Azure Pack, for example, is compatible with Windows Server 2016 and will be support until 2027.

So in short, **Azure Stack is for you when**:

* You want to adopt the cloud model and focus on delivering services instead on building and operating infrastructures (no DIY infrastructure)
* You want to develop or run modern cloud applications based on Azure services
* But you cannot go to Azure (because of regulations, latency, bad connectivity, etc.)

**Azure Stack is not your platform when**:

* You need traditional virtualization or even physical servers
* You do not want or you cannot adopt the cloud model and use public cloud or Azure at all
* You have a lot of legacy application which have the need for old operating systems (2008, 2008 R2, 2012…)

### So, I need one. Where can I get it and what does it cost?

First, you must select your preferred hardware vendor. Today you have the choice between HPE, Dell EMC or Lenovo. In the future, also systems from Cisco and Huawei will be available. When you have selected a hardware vendor you must decide which size of the integrated system you need. Currently configuration with 4, 8 or 12 nodes are available which cannot be extended in the first 6 Month. After that Microsoft promises to come up with an update which add the functionality to extend the Azure Stack integrated systems.

After you have chosen your preferred vendor and size you will order the integrated system (hardware) directly from the hardware vendor and the hardware pricing is defined by the hardware vendor.

When it comes to licensing cost, Azure Stack works the same as Azure which means you pay only what you use (pas-as-you-use). That means every service and every VM you are provisioning on Azure Stack will be billed on hours or transactions base. Exactly like it is in public Azure. However, the prices are a bit lower because you already payed for hardware, power, connectivity etc. For completely disconnected Azure Stack setup, Microsoft offers also a “capacity model”, which allows you to license the whole capacity at once. This way you will pay a fixed yearly fee, based on the counts of physical cores in your system. For more details about the prices the [pricing datasheet](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) from Microsoft gives you a great overview.