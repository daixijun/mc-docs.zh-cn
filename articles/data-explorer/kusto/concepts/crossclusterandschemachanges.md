---
title: Kusto 跨群集查询和架构更改 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的跨群集查询和架构更改。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 01/22/2021
ms.openlocfilehash: 0110ba831edb1ad61ff740625b9425b0687a0d23
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611657"
---
# <a name="cross-cluster-queries-and-schema-changes"></a>跨群集查询和架构更改

执行跨群集查询时，进行初始查询解释的群集必须具有远程群集中引用的实体的架构。
当以下查询发送到 Cluster1 时

```kusto
Table1 | join ( cluster("Cluster2").database("SomeDB").Table2 ) on KeyColumn
``` 

Cluster1 必须知道 Cluster2 上的 Table2 有哪些列以及这些列的数据类型。 若要获取该信息，需要将特殊命令从 Cluster1 发送到 Cluster2。
发送命令的开销可能很高。 检索到后，将缓存远程实体的架构，并且引用相同实体的下一个查询将运行较少的网络操作。

远程实体架构的更改可能会导致不利影响。 例如，无法识别添加的列，或者删除的列会导致“部分查询错误”而不是语义错误。

为了解决这个问题，缓存的架构会在检索到后一小时过期，因此在更改后超过一小时才执行的任何查询都将使用最新架构。

> [!IMPORTANT]
> 如果群集位于不同的租户中，则可能需要编辑 `trustedExternalTenants` 属性。 不受信任的外部租户可能会出现“未经授权的错误(401)”失败。 有关详细信息，请参阅[如何允许其他租户的主体访问群集](../../cross-tenant-query-and-commands.md)。
