---
title: 连接体系结构 - Azure Database for PostgreSQL - 单一服务器
description: 介绍 Azure Database for PostgreSQL 的连接体系结构 - 单一服务器。
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 05/23/2019
ms.date: 01/11/2021
ms.openlocfilehash: c85827fe50edbd3566baf71f326c6607f20bdae2
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98022138"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL 中的连接体系结构
本文介绍 Azure Database for PostgreSQL 的连接体系结构，以及如何在 Azure 内部和外部将流量从客户端定向到 Azure Database for PostgreSQL 数据库实例。

## <a name="connectivity-architecture"></a>连接体系结构
可以通过网关连接到 Azure Database for PostgreSQL，该网关负责将传入连接路由到服务器在群集中的物理位置。 下图演示了流量流。

:::image type="content" source="./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png" alt-text="连接体系结构概述":::


当客户端连接到数据库时，指向服务器的连接字符串将解析为网关 IP 地址。 网关在端口 5432 上侦听 IP 地址。 在数据库群集中，流量会转发到相应的 Azure Database for PostgreSQL。 因此，为了通过某种方式（例如，公司网络）连接到服务器，必须打开客户端防火墙，使出站流量能够到达我们的网关。 下面是一个按区域分类的可供我们的网关使用的 IP 地址的完整列表。

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Azure Database for PostgreSQL 网关 IP 地址

网关服务托管在一个 IP 地址后面的一组无状态计算节点上，当你的客户端尝试连接到 Azure Database for PostgreSQL 服务器时，将首先访问该 IP 地址。 

在持续的服务维护过程中，我们会定期刷新托管网关的计算硬件，以确保提供最安全和性能最佳的体验。 刷新网关硬件后，将首先生成一个新的计算节点通道。 这一新通道为所有新创建的 Azure Database for PostgreSQL 服务器提供流量，在同一区域中，它采用的 IP 地址将与较旧的网关通道采用的地址不同，目的在于使流量区分开来。 新通道完全正常运行后，为现有服务器提供服务的较旧的网关硬件将计划解除授权。 在解除网关硬件的授权之前，运行其服务器并连接到较旧网关通道的客户可通过电子邮件和 Azure 门户提前三个月收到通知。 如果你在应用程序的连接字符串中硬编码网关 IP 地址， 

* 网关的解除授权可能会影响与服务器的连接。 不建议这样做。应在应用程序的连接字符串中使用服务器的完全限定的域名 (FQDN)，格式为 <servername>.postgres.database.chinacloudapi.cn。 
* 请勿为使出站流量能够到达新的网关通道而在客户端防火墙中更新较新的网关 IP 地址。

下表列出了所有数据区域的 Azure Database for PostgreSQL 网关的网关 IP 地址。 下表中保留了每个区域的网关 IP 地址的最新信息。 在下表中，列表示以下内容：

| 区域名称 | **网关 IP 地址** |
|:----------------|:-------------------------|
| 中国东部 | 139.219.130.35    |
| 中国东部 2 | 40.73.82.1  |
| 中国北部 | 139.219.15.17    |
| 中国北部 2 | 40.73.50.0     |
||

## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建和管理 Azure Database for PostgreSQL 防火墙规则](./howto-manage-firewall-using-portal.md)
* [使用 Azure CLI 创建和管理 Azure Database for PostgreSQL 防火墙规则](./howto-manage-firewall-using-cli.md)
