---
title: 主版本升级 - Azure 门户 - Azure Database for MySQL 单一服务器
description: 本文介绍了如何使用 Azure 门户升级 Azure Database for MySQL 单一服务器的主版本。
author: WenJason
ms.author: v-jay
ms.service: mysql
ms.topic: how-to
origin.date: 11/16/2020
ms.date: 12/14/2020
ms.openlocfilehash: 8f49b14e3f1acf27152ce5ca6edaa792c27ec462
ms.sourcegitcommit: a8afac9982deafcf0652c63fe1615ba0ef1877be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96850906"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>使用 Azure 门户在 Azure Database for MySQL 单一服务器中升级主版本

> [!IMPORTANT]
> Azure Database for MySQL 单一服务器的主版本升级为公共预览版。

本文介绍了如何就地升级 Azure Database for MySQL 单一服务器中的 MySQL 主版本。

利用此功能，客户可以轻松地将 MySQL 5.6 服务器就地升级到 MySQL 5.7，无需移动任何数据，也无需更改应用程序连接字符串。

> [!Note]
> * 主版本升级仅适用于从 MySQL 5.6 到 MySQL 5.7 的升级<br>
> * 副本服务器尚不支持主版本升级。
> * 在整个升级操作过程中，服务器将不可用。 因此，建议在计划内维护时段执行升级。

## <a name="prerequisites"></a>先决条件
若要完成本操作指南，需要：
- [Azure Database for MySQL 单一服务器](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>执行从 MySQL 5.6 到 MySQL 5.7 的主版本升级

按照以下步骤为你的 Azure Database for MySQL 5.6 服务器执行主版本升级

> [!IMPORTANT]
> 建议首先在还原后的服务器副本上执行升级，而不是直接升级生产服务器。 请参阅[如何执行时间点还原](howto-restore-server-portal.md#point-in-time-restore)。 

1. 在 [Azure 门户](https://portal.azure.cn/)中，选择你的现有 Azure Database for MySQL 5.6 服务器。

2. 从“概述”页上，单击工具栏中的“升级”按钮。

3. 在“升级”部分，选择“确定”以将 Azure database for MySQL 5.6 服务器升级为 5.7 服务器。

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL - 概述 - 升级":::

4. 一条通知会确认升级是否成功。

## <a name="frequently-asked-questions"></a>常见问题

1\.我们的生产环境中有需要升级的 MySQL v5.6，此升级功能什么时候将正式发布？

计划在 MySQL v5.6 停用之前正式发布此功能。 但是，此功能已准备好用于生产环境，并且由 Azure 提供完全支持，因此你应该可以放心地在你的环境中运行它。 强烈建议你首先在还原后的服务器副本上运行和测试它，以便你可以估计升级期间的停机时间，并执行应用程序兼容性测试，然后再在生产环境中运行应用程序。这是推荐的最佳做法。 有关详细信息，请参阅[如何执行时间点还原](howto-restore-server-portal.md#point-in-time-restore)（用于创建服务器的时间点副本）。 

2\.这是否会导致服务器停机？如果是，会停机多长时间？

是的，服务器在升级过程中将不可用。因此，我们建议你在计划内维护时段执行此操作。 估计的停机时间取决于数据库大小、预配的存储大小（预配的 IOPS）以及数据库中的表的数量。 升级时间与服务器中表的数量成正比。基本 SKU 服务器的升级预计需要更长时间，因为它在标准存储平台上。 为了估计服务器环境的停机时间，建议你首先在还原后的服务器副本上执行升级。  

3\.请注意，副本服务器尚不支持此功能。这具体意味着什么？

当前，副本服务器不支持主版本升级，这意味着不应为复制所涉及的服务器（源服务器或副本服务器）运行主版本升级。 如果要在添加对升级功能的副本支持之前测试复制中涉及的服务器的升级，建议执行以下步骤：

1. 在计划内维护期间，在捕获副本服务器的名称和所有配置信息（防火墙设置、服务器参数配置，如果它与源服务器不同）后[停止复制并删除副本服务器](howto-read-replicas-portal.md)。
2. 执行源服务器的升级。
3. 使用在步骤 1 中捕获的名称和配置设置来预配新的只读副本服务器。 将源服务器升级到 v5.7 后，新的副本服务器会自动为 v5.7。

4\.如果未选择在 2021 年 2 月 5 日之前升级 MySQL 5.6 服务器，会发生什么情况？

你仍可像以前一样继续运行 MySQL v5.6 服务器。 Azure 永远不会在服务器上执行强制升级。 但是，会应用 [Azure Database for MySQL 版本控制策略](concepts-version-policy.md)中所述的限制。

## <a name="next-steps"></a>后续步骤

了解 [Azure Database for MySQL 版本控制策略](concepts-version-policy.md)。
