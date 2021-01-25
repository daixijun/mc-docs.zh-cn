---
title: 在 Azure 资源管理器中清理区容器
description: 本文介绍 Azure 数据资源管理器中的 .clean extent containers 命令
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: vrozov
ms.service: data-explorer
ms.topic: reference
origin.date: 01/17/2021
ms.date: 01/22/2021
ms.openlocfilehash: e013facbca3144840dc846333abc58f0b1daf72f
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614883"
---
# <a name="clean-extent-containers-commands"></a>清理区容器命令

本文介绍 Azure 数据资源管理器中的 `.clean databases extentcontainers` 和 `.show database extentcontainers clean operations` 命令。

## <a name="clean-databases-extentcontainers"></a>.clean databases extentcontainers

`.clean databases extentcontainers` 命令删除[数据分片（区）](extents-overview.md)上的维护和后台操作剩余的未使用的存储项目。 该命令在群集的基础存储帐户上以后台方式运行。 可以针对群集中数据库的特定列表或所有数据库调用此命令。 对于每个数据库，都会初始化一个单独操作，使用 [`.show database extentcontainers clean operations`](#show-database-extentcontainers-clean-operations) 命令可以监视这些操作。

必须有群集管理员或数据库管理员权限才能执行此命令。 有关详细信息，请参阅 [Kusto 中基于角色的授权](access-control/role-based-authorization.md)。

> [!WARNING]
> 运行 `.clean databases extentcontainers` 命令之后，[保留策略](../management/retentionpolicy.md)中定义的可恢复性会重置为该命令启动的时间。 数据库状态无法更改为较早的时间点。 最好只根据 Azure 顾问的建议来运行此命令。

### <a name="syntax"></a>语法

`.clean` `databases` `extentcontainers`
<br>

`.clean` `databases`  `(`DatabaseName1`,`...`,`DatabaseNameN`)`  `extentcontainers` 

### <a name="example"></a>示例

```kusto
.clean databases extentcontainers

.clean databases (DB1, DB2) extentcontainers
```

### <a name="output"></a>输出

| 输出参数                     | 类型    | 说明                                                                                                        |
|--------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------|
| OperationId                          | Guid    | 操作 ID。                                                                                                  |
| LastUpdatedOn                        | DateTime| 上次操作更新的日期/时间。                                                                       |
| 数据库                             | 字符串  | 已激活操作的数据库的名称。                                                     |
| DatabaseMajorVersion                 | Integer | 以其为基础来完成清理的数据库的主版本。                                         |
| DatabaseManorVersion                 | Integer | 以其为基础来完成清理的数据库的次要版本。                                         |
| 数据库                             | Guid    | 已激活操作的数据库的 ID。                                                       |
| 状态                                | String  | 操作的状态。                                                                                        |

## <a name="show-database-extentcontainers-clean-operations"></a>.show database extentcontainers clean operations

此命令在数据库级别监视 [`.clean databases extentcontainers`](#clean-databases-extentcontainers) 操作。

必须有群集管理员、群集监视者或数据库管理员权限才能执行此命令。 有关详细信息，请参阅 [Kusto 中基于角色的授权](access-control/role-based-authorization.md)。

### <a name="syntax"></a>语法

`.show` `database` DatabaseName1 `extentcontainers` `clean` `operations`
<br>
 
`.show` `database` DatabaseName1 `extentcontainers` `clean` `operations` *operation_id* 

### <a name="example"></a>示例

```kusto
.show database DB1 extentcontainers clean operations 

.show database DB1 extentcontainers clean operations 674d33e4-1a61-4bfb-a8d9-1378a90a56db
```

### <a name="output"></a>输出

| 输出参数                     | 类型    | 说明                                                                                                        |
|--------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------|
| OperationId                          | Guid    | 操作 ID。                                                                                                  |
| LastUpdatedOn                        | DateTime| 上次操作更新的日期/时间。                                                                       |
| 数据库                             | 字符串  | 已激活操作的数据库的名称。                                                     |
| DatabaseVersion                      | 字符串  | 以其为基础完成了清理的数据库的版本。                                               |
| 状态                                | String  | 操作的状态。                                                                                        |

