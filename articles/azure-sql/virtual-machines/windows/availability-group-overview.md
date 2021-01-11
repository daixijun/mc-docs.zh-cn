---
title: SQL Server Always On 可用性组概述
description: 本文介绍 Azure 虚拟机上的 SQL Server Always On 可用性组。
services: virtual-machines
documentationCenter: na
author: WenJason
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
origin.date: 10/07/2020
ms.date: 01/04/2021
ms.author: v-jay
ms.custom: seo-lt-2019
ms.openlocfilehash: ff0044f585cfdb33e76653eb9869908be272461f
ms.sourcegitcommit: cf3d8d87096ae96388fe273551216b1cb7bf92c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829763"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Azure VM 上的 SQL Server 的 Always On 可用性组
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍 Azure 虚拟机 (VM) 上 SQL Server 的 Always On 可用性组。 

## <a name="overview"></a>概述

Azure 虚拟机上的 Always On 可用性组类似于[本地的 Always On 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)。 但是，由于虚拟机托管在 Azure 中，还存在其他一些注意事项，例如 VM 冗余以及 Azure 网络上的流量路由。 

下图演示了 Azure VM 上的 SQL Server 可用性组：

![可用性组](./media/availability-group-overview/00-EndstateSampleNoELB.png)


## <a name="vm-redundancy"></a>VM 冗余 

若要提高冗余和可用性，SQL Server VM 应位于相同的[可用性集](../../../virtual-machines/windows/tutorial-availability-sets.md#availability-set-overview)中。

这可防止在部署推出期间影响组中的多个资源。 


## <a name="connectivity"></a>连接 

在传统的本地部署中，客户端使用虚拟网络名称 (VNN) 连接到可用性组侦听器，该侦听器将流量路由到可用性组中相应的 SQL Server 副本。 但是，在 Azure 网络上路由流量需满足其他要求。 

通过 Azure VM 上的 SQL Server，配置一个[负载均衡器](availability-group-vnn-azure-load-balancer-configure.md)来将流量路由到可用性组侦听器。如果你在使用 SQL Server 2019 CU8 及更高版本，可配置一个[分布式网络名称 (DNN) 侦听器](availability-group-distributed-network-name-dnn-listener-configure.md)来取代传统的 VNN 可用性组侦听器。 


### <a name="vnn-listener"></a>VNN 侦听器 

使用 [Azure 负载均衡器](../../../load-balancer/load-balancer-overview.md)将流量从侦听器路由到 Azure 网络上的传统可用性组虚拟网络名称 (VNN) 侦听器。 

负载均衡器保留 VNN 侦听器的 IP 地址。 如果有多个可用性组，则每个组都需要一个 VNN 侦听器。 一个负载均衡器可以支持多个侦听器。

若要开始，请参阅[配置负载均衡器](availability-group-vnn-azure-load-balancer-configure.md)。 

### <a name="dnn-listener"></a>DNN 侦听器

SQL Server 2019 CU8 引入了对分布式网络名称 (DNN) 侦听器的支持。 DNN 侦听器取代了传统的可用性组侦听器，消除了使用 Azure 负载均衡器在 Azure 网络上路由流量的需求。 

DNN 侦听器是 Azure 中推荐的 HADR 连接解决方案，它可简化部署、减少维护量和成本，同时减少在出现故障时进行故障转移的时间。 

请使用 DNN 侦听器来替代现有的 VNN 侦听器，或者将它与现有的 VNN 侦听器一起使用，使你的可用性组有两个不同的连接点 - 一个使用 VNN 侦听器名称（如果非默认，则还使用 VNN 侦听器端口），而另一个使用 DNN 侦听器名称和端口。 如果客户想要避免负载均衡器故障转移延迟，但仍使用依赖于 VNN 侦听器的 SQL Server 功能，例如分布式可用性组、服务代理或文件流，则这非常有用。 若要了解详细信息，请参阅 [DNN 侦听器和 SQL Server 功能互操作性](availability-group-dnn-interoperability.md)

若要开始，请参阅[配置DNN 侦听器](availability-group-distributed-network-name-dnn-listener-configure.md)。


## <a name="considerations"></a>注意事项 

在 Azure IaaS VM 来宾故障转移群集上，建议为每个服务器（群集节点）设置一个 NIC，并且只使用一个子网。 Azure 网络具有物理冗余，这使得在 Azure IaaS VM 来宾群集上不需要额外的 NIC 和子网。 虽然群集验证报告将发出警告，指出节点只能在单个网络上访问，但在 Azure IaaS VM 来宾故障转移群集上可以安全地忽略此警告。 

## <a name="next-steps"></a>后续步骤

查看 [HADR 最佳做法](hadr-cluster-best-practices.md)，然后开始[手动](availability-group-manually-configure-prerequisites-tutorial.md)部署可用性组。

或者，可部署[无群集的可用性组](availability-group-clusterless-workgroup-configure.md)或者[多个区域](availability-group-manually-configure-multiple-regions.md)中的可用性组。

<!-- Update_Description: new article about availability group overview -->
<!--NEW.date: 07/06/2020-->