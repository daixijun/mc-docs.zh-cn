---
title: 没有 800 计数限制的资源
description: 列出可以在一个资源组中有 800 多个实例的 Azure 资源类型。
ms.topic: conceptual
origin.date: 10/28/2020
author: rockboyfor
ms.date: 01/11/2021
ms.testscope: no
ms.testdate: ''
ms.author: v-yeche
ms.openlocfilehash: cc59f041bd34881d2afbf01ec6676ceaf0c155a2
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022921"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>没有每个资源组 800 个实例限制的资源

默认情况下，就单个资源类型来说，可以在每个资源组中部署最多 800 个实例。 不过，某些资源类型没有 800 个实例的限制。 本文列出了可以在一个资源组中有 800 多个实例的 Azure 资源类型。 所有其他资源类型有 800 个实例的限制。

就某些资源类型来说，若要移除 800 个实例的限制，需联系支持部门。 这些资源类型已在本文中注明。

## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* edgeSubscriptions
* linkedSubscriptions
* registrations
* registrations/customerSubscriptions
* registrations/products

<!--Not Available on ## Microsoft.BotService-->

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* galleries
* galleries/images
* galleries/images/versions
* images
* snapshots
* virtualMachineScaleSets - 默认情况下，限制为 800 个实例。 可以通过联系支持人员来提高该限制。
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* registries/buildTasks/listSourceRepositoryProperties
* registries/buildTasks/steps
* registries/buildTasks/steps/listBuildArguments
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

<!--Not Available on ## Microsoft.D365CustomerInsights-->

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* flexibleServers
* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* flexibleServers
* serverGroups
* servers
* serversv2

<!--Not Available on ## Microsoft.DevTestLab-->
<!--Not Available on ## Microsoft.EnterpriseKnowledgeGraph-->

## <a name="microsofteventhub"></a>Microsoft.EventHub

* clusters
* namespaces

<!--Not Available on ## Microsoft.Experimentation-->

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftmedia"></a>Microsoft.Media

* mediaservices/liveEvents

<!--Not Available on ## Microsoft.NetApp-->

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
    
    <!--Not Available on * ddosProtectionPlans-->
    
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/all
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses - 默认情况下，限制为 800 个实例。 可以通过联系支持人员来提高该限制。
* serviceEndpointPolicies
* trafficmanagerprofiles

<!--Not Available on * virtualNetworkTaps-->

<!--Not Available on ## Microsoft.PortalSdk-->

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections - 默认情况下，限制为 800 个实例。 可以通过联系支持人员来提高该限制。

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

* capacities - 默认情况下，限制为 800 个实例。 可以通过联系支持人员来提高该限制。

## <a name="microsoftrelay"></a>Microsoft.Relay

* namespaces

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* namespaces

<!--Not Avaiable on ## Microsoft.Singularity-->

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftsql"></a>Microsoft.Sql

* servers/databases

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="next-steps"></a>后续步骤

有关配额和限制的完整列表，请参阅 [Azure 订阅和服务限制、配额与约束](azure-subscription-service-limits.md)。

<!-- Update_Description: update meta properties, wording update, update link -->
