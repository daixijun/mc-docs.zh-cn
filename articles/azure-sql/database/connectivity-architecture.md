---
title: Azure SQL 数据库连接体系结构
description: 本文档介绍了用于从 Azure 内部或 Azure 外部进行数据库连接的 Azure SQL 数据库连接体系结构。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: sstein, vanto
origin.date: 06/26/2020
ms.date: 12/14/2020
ms.openlocfilehash: 5aac517cadeba30ba2a5c8453a35ad4a5a0bda74
ms.sourcegitcommit: cf3d8d87096ae96388fe273551216b1cb7bf92c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2020
ms.locfileid: "97830167"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Azure SQL 数据库和 Azure Synapse Analytics 连接体系结构
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

本文介绍了将网络流量定向到 Azure SQL 数据库或 Azure Synapse Analytics 的服务器的各种组件的体系结构。 它还介绍了不同的连接策略，以及这些策略如何影响从 Azure 内部连接的客户端以及从 Azure 外部连接的客户端。

> [!IMPORTANT]
> 本文不适用于 **Azure SQL 托管实例**。 请参阅 [托管实例的连接体系结构](../managed-instance/connectivity-architecture-overview.md)。

## <a name="connectivity-architecture"></a>连接体系结构

下图提供连接体系结构的综合概述。

![此图提供连接体系结构的概览。](./media/connectivity-architecture/connectivity-overview.png)

以下步骤介绍如何建立与 Azure SQL 数据库的连接：

- 客户端连接到网关，后者使用公共 IP 地址并侦听端口 1433。
- 该网关根据有效的连接策略将流量重定向或代理到适当的数据库群集。
- 在数据库群集中，流量转发到相应的数据库。

## <a name="connection-policy"></a>连接策略

SQL 数据库和 Azure Synapse 中的服务器支持以下三个服务器连接策略设置选项：

- **重定向（建议）：** 客户端直接与托管数据库的节点建立连接，从而降低延迟并改进吞吐量。 若要通过连接来使用此模式，客户端需要：
  - 在范围为 11000 到 11999 的端口上允许从客户端到区域中所有 Azure SQL IP 地址的出站通信。 使用 SQL 服务标记，使其更易于管理。  
  - 在端口 1433 上允许从客户端到 Azure SQL 数据库网关 IP 地址的出站通信。

- **代理：** 在此模式下，所有连接都通过 Azure SQL 数据库网关来代理，导致延迟增大和吞吐量降低。 若要通过连接来使用此模式，客户端需满足以下条件：在端口 1433 上允许从客户端到 Azure SQL 数据库网关 IP 地址的出站通信。

- 默认值：除非显式将连接策略更改为 `Proxy` 或 `Redirect`，否则，在创建后，此连接策略将在所有服务器上生效。 对于所有源自 Azure 内部的客户端连接（例如，源自 Azure 虚拟机的连接），默认策略为 `Redirect`；对于所有源自外部的客户端连接（例如，源自本地工作站的连接），默认策略为 `Proxy`。

我们强烈建议使用 `Redirect` 连接策略而不要使用 `Proxy` 连接策略，以最大程度地降低延迟和提高吞吐量。 但是，你需要满足上述允许网络流量的附加要求。 如果客户端为 Azure 虚拟机，则可将网络安全组 (NSG) 与[服务标记](../../virtual-network/network-security-groups-overview.md#service-tags)配合使用来实现它。 如果客户端从本地工作站进行连接，则可能需要联系网络管理员，让其允许网络流量通过公司防火墙。

## <a name="connectivity-from-within-azure"></a>从 Azure 内连接

如果从 Azure 内部连接，则连接默认具有 `Redirect` 连接策略。 `Redirect` 策略是指建立到 Azure SQL 数据库的 TCP 会话连接后，会将 Azure SQL 数据库网关的目标虚拟 IP 更改为群集的目标虚拟 IP，从而将客户端会话重定向到适当的数据库群集。 此后，所有后续数据包绕过 Azure SQL 数据库网关，直接传输到群集。 下图演示了此流量流。

![体系结构概述](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>从 Azure 外连接

如果从 Azure 外部连接，则连接默认具有 `Proxy` 连接策略。 `Proxy` 策略是指通过 Azure SQL 数据库网关建立 TCP 会话，并且所有后续数据包通过网关传输。 下图演示了此流量流。

![此图显示如何通过 Azure SQL 数据库网关建立 TCP 会话，以及所有后续数据包如何流经网关。](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> 另请打开 TCP 端口 1434 和 14000-14999，以便[使用 DAC 进行连接](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>网关 IP 地址

下表按区域列出了网关的 IP 地址。 若要连接到 SQL 数据库或 Azure Synapse，需要允许前往/来自该区域的所有网关的网络流量。

| 区域名称          | 网关 IP 地址 |
| --- | --- |
| 中国东部           | 139.219.130.35     |
| 中国东部 2         | 40.73.82.1         |
| 中国北部          | 139.219.15.17      |
| 中国北部 2        | 40.73.50.0         |

## <a name="next-steps"></a>后续步骤

- 有关如何更改服务器的 Azure SQL 数据库连接策略的信息，请参阅 [conn-policy](https://docs.azure.cn/cli/sql/server/conn-policy)。
- 若要了解使用 ADO.NET 4.5 或更高版本的客户端的 Azure SQL 数据库连接行为，请参阅[用于 ADO.NET 4.5 的非 1433 端口](adonet-v12-develop-direct-route-ports.md)。
- 若要了解常规应用程序开发的概述信息，请参阅[SQL 数据库应用程序开发概述](develop-overview.md)。
