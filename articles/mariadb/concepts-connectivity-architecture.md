---
title: 连接体系结构 - Azure Database for MariaDB
description: 介绍 Azure Database for MariaDB 服务器的连接体系结构。
author: WenJason
ms.author: v-jay
ms.service: mariadb
ms.topic: conceptual
origin.date: 6/8/2020
ms.date: 01/11/2021
ms.openlocfilehash: 3c18cd4925571fcdc3b7320f7c6d5cbd56f145e9
ms.sourcegitcommit: 79a5fbf0995801e4d1dea7f293da2f413787a7b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98023091"
---
# <a name="connectivity-architecture-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的连接体系结构
本文介绍 Azure Database for MariaDB 的连接体系结构，以及如何在 Azure 内部和外部将流量从客户端定向到 Azure Database for MariaDB 实例。

## <a name="connectivity-architecture"></a>连接体系结构

通过网关建立与 Azure Database for MariaDB 的连接，该网关负责将传入连接路由到群集中服务器的物理位置。 下图演示了流量流。

![连接体系结构概述](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)


当客户端连接到数据库时，指向服务器的连接字符串将解析为网关 IP 地址。 网关侦听端口 3306 上的 IP 地址。 在数据库群集中，流量将转发到相应的 Azure Database for MariaDB。 因此，为了通过某种方式（例如通过公司网络）连接到服务器，必须打开客户端防火墙，使出站流量能够访问我们的网关。 下面是一个按区域分类的可供我们的网关使用的 IP 地址的完整列表。

## <a name="azure-database-for-mariadb-gateway-ip-addresses"></a>Azure Database for MariaDB 网关 IP 地址

网关服务托管在一个 IP 地址后面的一组无状态计算节点上，当你的客户端尝试连接到 Azure Database for MariaDB 服务器时，将首先访问该 IP 地址。 

在持续的服务维护过程中，我们会定期刷新托管网关的计算硬件，以确保提供最安全和性能最佳的体验。 刷新网关硬件后，将首先生成一个新的计算节点环。 这一新环为所有新创建的 Azure Database for MariaDB 服务器提供流量，在同一区域中，它具有与旧网关环不同的 IP 地址，以使流量区分开来。 新环完全正常运行后，为现有服务器提供服务的较旧的网关硬件将计划解除授权。 在解除网关硬件的授权之前，运行其服务器并连接到较旧网关环的客户将通过电子邮件和 Azure 门户提前三个月收到通知。 如果你在应用程序的连接字符串中对网关 IP 地址进行硬编码， 

* 网关的解除授权可能会影响与服务器的连接。 不建议这样做。 应在应用程序的连接字符串中使用服务器的完全限定的域名 (FQDN)，其格式为 <servername>.mariadb.database.chinacloudapi.cn。 
* 请勿为使出站流量能够到达新的网关环而在客户端防火墙中更新较新的网关 IP 地址。

下表列出了所有数据区域的 Azure Database for MariaDB 网关的网关 IP 地址。 下表中保留了每个区域的网关 IP 地址的最新信息。 在下表中，列表示以下内容：

| **区域名称** | **网关 IP 地址** |
|:----------------|:-------------------------|
| 中国东部 | 139.219.130.35    |
| 中国东部 2 | 40.73.82.1  |
| 中国北部 | 139.219.15.17    |
| 中国北部 2 | 40.73.50.0     |
||

## <a name="connection-redirection"></a>连接重定向

Azure Database for MariaDB 支持一个额外的连接策略（即“重定向”），该策略有助于降低客户端应用程序与 MariaDB 服务器之间的网络延迟。 利用此功能，在建立与 Azure Database for MariaDB 服务器的初始 TCP 会话后，服务器会将承载 MariaDB 服务器的节点的后端地址返回到客户端。 此后，所有后续数据包会绕过网关直接流向服务器。 由于数据包会直接流向服务器，因此延迟和吞吐量这两个指标的表现得到了改善。

引擎版本为 10.2 和 10.3 的 Azure Database for MariaDB 服务器支持此功能。

对重定向的支持可通过 Microsoft 开发的 PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 扩展获得，也可在 [PECL](https://pecl.php.net/package/mysqlnd_azure) 上获得。 有关如何在应用程序中使用重定向的详细信息，请参阅[配置重定向](./howto-redirection.md)一文。

> [!IMPORTANT]
> PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 扩展对重定向的支持目前为预览版。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 门户创建和管理 Azure Database for MariaDB 防火墙规则](./howto-manage-firewall-portal.md)
* [使用 Azure CLI 创建和管理 Azure Database for MariaDB 防火墙规则](./howto-manage-firewall-cli.md)
