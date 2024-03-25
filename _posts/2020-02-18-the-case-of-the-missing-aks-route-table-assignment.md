---
title: The case of the missing AKS route table assignment
excerpt: "When an AKS (Azure Kubernetes Service) cluster is deployed with basic network (kubenet) a (UDR) route table gets created in Azure. But sometimes this route table gets not correctly assigned to the virtual network of the AKS cluster. This post describes why this can happen and what you can do against."
classes: wide
header:
  teaser: /assets/images/aks-routing-table.png
toc: true
toc_label: "In this post"
date: 2020-02-18
tags:
  - Microsoft Azure
  - AKS
  - Kubernetes
  - IaC
---
## TL;DR
Note: If you use the advanced networking (Azure CNI) option then this post is not relevant for you as in that case no route table and UDR is involved.
{: .notice}

By default an AKS cluster is created with the 'kubenet' aka. basic networking option which depends highly on Azure 'User Defined Routing' (UDR). Therefore during the deployment of the AKS cluster a route table Azure resource is automatically created and assigned to the virtual subnet of the AKS cluster worker nodes. This is how it works at least when you use all the defaults where the Azure virtual network is also managed and automatically created by AKS.

But if you want to use a Azure virtual network for AKS which you create forehand with IaC tools like Terraform or Azure Resource Manager (ARM), you will face some problems where the assignment of the route table is not correctly made or preserved during subsequently deployments of the IaC code.

To solve this you must either ignore the changes on the route table assignment on the Azure virtual network subnet in your IaC code or on every re-deployment read first the current state of the assignment and then pass the current value to your IaC code so that the assignment is preserved even you have not defined the assignment explicitly in your code. See [below](#possible-solutions) for examples how this can be done with Terraform or ARM templates.

A more in depth look at the AKS networking options can be found in the official Microsoft documentation at: [https://docs.microsoft.com/en-us/azure/aks/concepts-network#azure-virtual-networks](https://docs.microsoft.com/en-us/azure/aks/concepts-network#azure-virtual-networks)

## What is the route table for

To understand the purpose of the route table we need to have look to the high level architecture of an AKS cluster first.

{% include figure image_path="/assets/images/aks-routing-table.png" alt="AKS Kubenet overview" caption="Source: Microsoft" %}

Basically seen on a very simple level an AKS cluster consists of the following three main parts:

* A Kubernetes master node which is actually represented by the Kubernetes service Azure resource. (not shown in the picture)
* Multiple Kubernetes worker nodes which get provisioned automatically by ASK as Azure virtual machines. (Node1 and Node2)
* An Azure virtual network with at least one subnet in which the worker nodes resides.

When looking at the picture above we can see that actually only the worker nodes get an IP address form the Azure virtual network subnet itself. The Kubernetes Pods running on the nodes will get an IP address from a subnet, managed by AKS resp. Kubernetes itself, which is logically completely different and independent to the Azure virtual network. Because of these different subnets a Pod would actually not be able to reach resources in the Azure virtual network and therefore also no other Pods running on another node.

To overcome this limitation, a network address translation (NAT) gets configured on every node so that when a Pod wants to communicate outside of his node the Pods IP address (the source IP address) gets rewritten to the IP address of the node itself. This however solves still only a part of the communication issues. The Pod can now reach the Azure virtual network subnet but the Pod can still not reach another Pod on another node because the Azure virtual network does still not know how to reach the IP subnet of the Pods on the other nodes. This is where Azure User Defined Routing (UDR) and the route table resource comes into play. In the route table resource the AKS service adds automatically a route for every Pod subnet of every node with the respectively node IP as the next hop. So for the example in the picture the route table would have following two route entries:

* 10.244.0.0/24 -> Next Hop = 192.168.1.4
* 10.244.1.4/24 -> Next Hop = 192.168.1.5

These custom routes allows then the Pods on one node to communicate across the Azure virtual network with Pods running on the other nodes although the Pod IP addresses are actually not part of the address range of the Azure virtual network.

> **Note:** This little "trick" with the route table is also the reason for the maximum number of nodes of an AKS cluster with kubenet networking. An Azure UDR route table supports a maximum of 400 routes, thus the cluster can not have more than 400 nodes.
{: .notice--info}

## So where can I find this route table

When you look in the Azure Portal (or even with CLI or PowerShell) after the route table resource of an AKS cluster you may get some question marks. When you look in your resource group where you have initially deployed AKS you see nothing else than the Kubernetes service resource it self but there is no trace of a route table resource. This is because every AKS cluster creates an additional resource group to which it deploys the resources managed by itself. These are for examples the virtual machines for the worker nodes, a Azure virtual network and also the famous route table.
You can locate the automatically created, 'node resource group' in the same subscription simply by its name which is always in the format `mc_<your RG name>_<AKS cluster name>_<region>`. Alternatively you can query the name of the resource group with the following Azure CLI command: `az aks show --name <AKS cluster name> --resource-group <your RG name> --query nodeResourceGroup`.

> **Note:** If the route table resource does not exist at all in the node resource group (the RG with the 'mc_' prefix in the name) then you are facing another problem during the initial provisioning of the AKS cluster. The problems described here are these where the AKS cluster is successfully deployed and the route table actually exists but is simply not connected/assigned to the AKS nodes subnet.
{: .notice--warning}

## That's fine, what could possibly go wrong
Well as long as you just deploy a default configuration with the basic/kubenet networking option selected you may have never a problem with the route table. The "fun" begins as soon as you want to use your own Azure virtual network for the AKS cluster and use IaC tools like Terraform or ARM templates for the deployment (what you of course should do). The main source of the problem is that the Azure virtual network is not anymore created and fully controlled by ASK itself, much more it is now created by you resp. your IaC code and AKS will just use a specified subnet in it for its worker nodes VMs. This often leads to the problem that the route table gets not assigned at all to the AKS nodes subnet or that the assignment get removed/overwritten in subsequently re-deployments of the IaC code resp. the Azure virtual network. But when the route table is not assigned to the AKS nodes subnet it will not have any effect and the routing between the Pods is broken which in turns leads to very strange errors and behaviors. While there are probably a tons of other reasons why the routing table not gets or keeps correctly assigned to the AKS nodes subnet, the following two are the most common which I experienced.

![](/assets/images/aks-routing-table-vnet.png)

### 1. The missing permission

The first problem you can ran into when using a custom Azure virtual network instead of the an automatically created one is that the Service Principal, which the AKS cluster is running under, has not enough permission to make any changes on the virtual network resp. the subnet. Most likely the AKS deployment will fail completely in this cases as it can not even deploy his nodes VMs because the AKS has not the permissions to join his worker nodes VMs to the subnet. The simplest way to fix this is to assign the built in RBAC role 'Network Contributor' to the Service Principal, used by the AKS cluster, on your virtual network.

More in depth details how to deploy an AKS with a custom virtual network can you find in the Azure documentation: [Use kubenet networking with your own IP address ranges in Azure Kubernetes Service (AKS)](https://docs.microsoft.com/en-us/azure/aks/configure-kubenet)

### 2. The route table assignment dilemma

#### The problem

This issue gave me really some headaches when I deployed the first time an AKS cluster using a custom Azure virtual network through IaC. The problem here is that the custom virtual network is also created by the IaC code along with the AKS cluster or right before the AKS cluster is deployed. But because the route table is created by AKS after the virtual network was created you can not specify the route table for the node subnet in your IaC code (a classic chicken and egg situation). So your IaC code has absolutely no clue about the route table which must be assigned to the AKS nodes subnet for the correct functionality of the AKS cluster.

While this is actually not a problem for the first run/deployment of the IaC code it is really a nasty thing for all subsequently deployments (e.g. when using a CI/CD pipeline). Every time changes are made to the IaC code and a re-deployed is made the IaC code will almost certainly (it depends a little bit of the used tooling) removes the route table assignment on the node subnet because this assignment is just not defined by the IaC code. And as soon as this happens the AKS will have massive communication issues which means "💩 hits the fan". Especially when this happens on an productive AKS cluster...

The problem is also widely discussed in an [issue on GitHub](https://github.com/Azure/AKS/issues/400). But I don't think it a problem which AKS has to be solved by it self as it is more a IaC problem than a AKS specific problem. Basically this is a problem that is not only restricted to AKS but always when you managing your resources actually by IaC but something or someone other makes also changes to the infrastructure resources without the use of IaC. Most of the time you want however exactly the effect that the IaC tool reverts all these "out of band" changes the next time the IaC code get deployed and that is also why most of the IaC tools work this way. But in the case of the route table assignment for an AKS cluster you definitely do not want that reversion to happen.

#### Possible solutions

So how to solve this? Basically there are two options. Either teach the used IaC tool to just ignore the change (the new route table association made by AKS) or make sure that somehow the current state of the virtual network and subnet gets read first and that then the incremental changes are applied during a re-deployment of the virtual network and subnet so that the route table association is preserved. Practically this is done:

* With Terraform by specify a life cycle policy on the subnet so tha any changes of `routetable_id` property is ignored by Terraform

  ``` terraform
    resource "azurerm_subnet" "kubesubnet" {
      ...
      lifecycle {
        ignore_changes = [
          "route_table_id"
      ]
    }
  }
  ```

* With a linked ARM template which first gets the current route table association on the subnet and then pass the output to the template which creates the virtual network and subnet. So that when the `routeTable` property is set on the subnet it gets preserved during a re-deployment of the ARM template Michele Tedesco from Microsoft Switzerland has posted on GitHub an example how to do that: [https://github.com/Azure/AKS/issues/400#issuecomment-515637751](https://github.com/Azure/AKS/issues/400#issuecomment-515637751)

Hope this helps you also to understand the functionality of the AKS route table or identify and resolve your issues with it when using a custom virtual network and IaC.

So long, have fun and always take care on your route tables! 😁
