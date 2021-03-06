---
title: Microsoft Azure Stack Proof Of Concept (POC) architecture | Microsoft Docs
description: View the Microsoft Azure Stack POC architecture.
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: helaw
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 48cfeec3f6b06b770bbc98683481d3d47b3851c3


---
# <a name="microsoft-azure-stack-poc-architecture"></a>Microsoft Azure Stack POC architecture
The Azure Stack POC is a one-node deployment of Azure Stack Technical Preview 2. All the components are installed in virtual machines running on a single host machine. 

## <a name="logical-architecture-diagram"></a>Logical architecture diagram
The following diagram illustrates the logical architecture of the Azure Stack POC and its components.

![](media/azure-stack-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Virtual machine roles
The Azure Stack POC offers services using the following VMs on the POC host:

* **MAS-ACS01** Virtual machine hosting Azure Stack storage services.
* **MAS-ADFS01** Virtual machine hosting Active Directory Federation Services.  This virtual machine is not used in Technical Preview 2.  
* **MAS-ASQL01**  Virtual machine providing an internal data store for Azure Stack infrastructure roles.  
* **MAS-BGPNAT01** Virtual Machine acting as an edge router and provides NAT and VPN capabilities for Azure Stack.
* **MAS-CA01** Virtual machine providing certificate authority services for Azure Stack role services.
* **MAS-CON01** Virtual machine available to developers for installing PowerShell, Visual Studio, and other tools.
* **MAS-DC01** Virtual machine hosting Active Directory, DNS, and DHCP services for Microsoft Azure Stack.
* **MAS-GWY01** Virtual machine providing edge gateway services such as VPN site-to-site connections for tenant networks.
* **MAS-NC01**  Virtual machine hosting Network Controller, which manages Azure Stack network services.  
* **MAS-SLB01**  Virtual machine provides load balancing services in Azure Stack for both tenants and Azure Stack infrastructure services.  
* **MAS-SUS01**  Virtual machine hosting Windows Server Update Services, and responsible for providing updates to other Azure Stack virtual machines.
* **MAS-WAS01**  Virtual machine hosting portal and Azure Resource Manager services.
* **MAS-XRP01** Virtual machine that hosts the core resource providers of Microsoft Azure Stack, including the Compute, Network, and Storage resource providers.

## <a name="storage-services"></a>Storage services
Storage services in the operating system on the physical host include:

* **ACS Blob Service** Azure Consistent Storage Blob service, which provides blob and table storage services.
* **SoFS** Scale-out File Server.
* **ReFS CSV** Resilient File System Cluster Shared Volume.
* **Virtual Disk**, **Storage Space**, and **Storage Spaces Direct** are the respective underlying storage technology in Windows Server to enable the Microsoft Azure Stack core storage resource provider.

## <a name="next-steps"></a>Next steps
[Deploy Azure Stack](azure-stack-deploy.md)

[First scenarios to try](azure-stack-first-scenarios.md)




<!--HONumber=Nov16_HO3-->


