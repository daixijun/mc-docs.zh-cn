---
title: 群集 follower 命令 - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍了 Azure 数据资源管理器中的群集 follower 命令。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 03/18/2020
ms.date: 01/22/2021
ms.openlocfilehash: 66be025b0ffa6f7d0ebe7e2a06f03b9e2aca134a
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611655"
---
# <a name="cluster-follower-commands"></a>群集 follower 命令

下面列出了用于管理随从群集配置的控制命令。 这些命令以同步方式运行，但在下一次定期架构刷新时应用。 因此，在新配置得到应用之前，可能会有几分钟的延迟。

follower 命令包括[数据库级命令](#database-level-commands)和[表级命令](#table-level-commands)。

## <a name="database-policy-overrides"></a>数据库策略替代

先导数据库可以替代随从群集中的以下数据库级策略：[缓存策略](#caching-policy)和[经授权的主体](#authorized-principals)。

### <a name="caching-policy"></a>缓存策略

随从群集的默认[缓存策略](cachepolicy.md)使用先导群集的数据库级和表级缓存策略。

|选项             |说明                                 |
|-------------------|----------------------------------------------|
|**无**（默认） |使用的缓存策略是在先导群集的源数据库中定义的那些策略。   |
|**replace**  |先导群集中的源数据库的数据库级和表级缓存策略会被删除（设置为 `null`）。 这些策略会被替换为数据库级和表级替代策略（如果已定义）。|
|**union**    |先导群集中的源数据库的数据库级和表级缓存策略将与数据库级和表级替代策略中定义的策略组合使用。   |

> [!NOTE]
>  * 如果替代数据库级和表级缓存策略的集合为空，则默认情况下会缓存所有内容。
>  * 可以将数据库级缓存策略替代设置为 `0d`，则默认情况下不会缓存任何内容。

### <a name="authorized-principals"></a>经授权的主体

|选项             |说明                                                                                                                              |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------|
|**无**（默认） |[经授权的主体](access-control/index.md#authorization)在先导群集的源数据库中定义。     |
|**replace**        |替代经授权的主体会替换先导群集中源数据库的经授权主体。  |
|**union**          |替代经授权的主体可与先导群集中源数据库的经授权主体组合使用。 |

> [!NOTE]
> 如果替代经授权的主体的集合为空，则不会有数据库级主体。

## <a name="table-policy-overrides"></a>表策略替代

默认情况下，后面跟有随从群集的数据库中的表会保留源表的缓存策略。 但是，可以在随从群集中替代表级[缓存策略](cachepolicy.md)。
请使用 `replace` 选项来替代源表的缓存策略。

## <a name="database-level-commands"></a>数据库级命令

### <a name="show-follower-database"></a>.show follower database

显示从其他先导群集追随的一个或多个数据库，它们配置了一个或多个数据库级替代项。

**语法**

`.show` `follower` `database` *DatabaseName*

`.show` `follower` `databases` `(`*DatabaseName1*`,`...`,`*DatabaseNameN*`)`

**输出** 

| 输出参数                     | 类型    | 说明                                                                                                        |
|--------------------------------------|---------|--------------------------------------------------------------------------------------------------------------------|
| DatabaseName                         | String  | 被追随的数据库的名称。                                                                           |
| LeaderClusterMetadataPath            | String  | 先导群集的元数据容器的路径。                                                               |
| CachingPolicyOverride                | String  | 数据库的替代缓存策略，已序列化为 JSON 或 null。                                         |
| AuthorizedPrincipalsOverride         | String  | 数据库的经授权主体的替代集合，已序列化为 JSON 或 null。                    |
| AuthorizedPrincipalsModificationKind | String  | 要使用 AuthorizedPrincipalsOverride（`none`、`union` 或 `replace`）来应用的修改类型。                  |
| CachingPoliciesModificationKind      | String  | 要使用数据库级或表级缓存策略替代（`none`、`union` 或 `replace`）来应用的修改类型。 |
| IsAutoPrefetchEnabled                | 布尔 | 在每次进行架构刷新时是否预先提取新数据。        |
| TableMetadataOverrides               | String  | 如果已定义此项，则此项是表级属性替代的 JSON 序列化形式。              |

### <a name="alter-follower-database-policy-caching"></a>.alter follower database policy caching

更改随从数据库缓存策略，以替代在先导群集中的源数据库上设置的策略。 它需要 [DatabaseAdmin 权限](../management/access-control/role-based-authorization.md)。

**说明**

* 缓存策略的默认 `modification kind` 为 `union`。 若要更改 `modification kind`，请使用 [`.alter follower database caching-policies-modification-kind`](#alter-follower-database-caching-policies-modification-kind) 命令。
* 可以使用以下 `.show` 命令查看策略或查看在更改后生效的策略：
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* 可以使用 [`.show follower database`](#show-follower-database) 查看在进行更改后的随从数据库上的替代设置

**语法**

`.alter` `follower` `database` *DatabaseName* `policy` `caching` `hot` `=` *HotDataSpan*

**示例**

```kusto
.alter follower database MyDb policy caching hot = 7d
```

### <a name="delete-follower-database-policy-caching"></a>.delete follower database policy caching

删除随从数据库替代缓存策略。 此删除操作会导致在先导群集中的源数据库上设置的策略成为生效的策略。
它需要 [DatabaseAdmin 权限](../management/access-control/role-based-authorization.md)。 

**说明**

* 可以使用以下 `.show` 命令查看策略或查看在更改后生效的策略：
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* 可以使用 [`.show follower database`](#show-follower-database) 查看在进行更改后的随从数据库上的替代设置

**语法**

`.delete` `follower` `database` *DatabaseName* `policy` `caching`

**示例**

```kusto
.delete follower database MyDB policy caching
```

### <a name="add-follower-database-principals"></a>.add follower database principals

将经授权的主体添加到替代经授权主体的随从数据库集合中。 它需要 [DatabaseAdmin 权限](../management/access-control/role-based-authorization.md)。

**说明**

* 此类经授权主体的默认 `modification kind` 为 `none`。 若要更改 `modification kind`，请使用 [alter follower database principals-modification-kind](#alter-follower-database-principals-modification-kind)。
* 可以使用以下 `.show` 命令查看在更改后生效的主体集合：
    * [`.show database principals`](../management/security-roles.md#managing-database-security-roles)
    * [`.show database details`](../management/show-databases.md)
* 可以使用 [`.show follower database`](#show-follower-database) 查看在进行更改后的随从数据库上的替代设置

**语法**

`.add` `follower` `database` *DatabaseName* (`admins` | `users` | `viewers` | `monitors`) Role `(`*principal1*`,`...`,`*principalN*`)` [`'`*notes*`'`]

**示例**

```kusto
.add follower database MyDB viewers ('aadgroup=mygroup@microsoft.com') 'My Group'
```

### <a name="drop-follower-database-principals"></a>.drop follower database principals

将经授权的主体从替代经授权主体的随从数据库集合中删除。
它需要 [DatabaseAdmin 权限](../management/access-control/role-based-authorization.md)。

**说明**

* 可以使用以下 `.show` 命令查看在更改后生效的主体集合：
    * [`.show database principals`](../management/security-roles.md#managing-database-security-roles)
    * [`.show database details`](../management/show-databases.md)
* 可以使用 [`.show follower database`](#show-follower-database) 查看在进行更改后的随从数据库上的替代设置

**语法**

`.drop` `follower` `database` *DatabaseName* (`admins` | `users` | `viewers` | `monitors`) `(`*principal1*`,`...`,`*principalN*`)`

**示例**

```kusto
.drop follower database MyDB viewers ('aadgroup=mygroup@microsoft.com')
```

### <a name="alter-follower-database-principals-modification-kind"></a>.alter follower database principals-modification-kind

更改随从数据库经授权主体修改类型。 它需要 [DatabaseAdmin 权限](../management/access-control/role-based-authorization.md)。

**说明**

* 可以使用以下 `.show` 命令查看在更改后生效的主体集合：
    * [`.show database principals`](../management/security-roles.md#managing-database-security-roles)
    * [`.show database details`](../management/show-databases.md)
* 可以使用 [`.show follower database`](#show-follower-database) 查看在进行更改后的随从数据库上的替代设置

**语法**

`.alter` `follower` `database` *DatabaseName*
`principals-modification-kind` = (`none` | `union` | `replace`)

**示例**

```kusto
.alter follower database MyDB principals-modification-kind = union
```

### <a name="alter-follower-database-caching-policies-modification-kind"></a>.alter follower database caching-policies-modification-kind

更改随从数据库和表缓存策略修改类型。 它需要 [DatabaseAdmin 权限](../management/access-control/role-based-authorization.md)。

**说明**

* 可以使用标准 `.show` 命令查看在更改后生效的数据库级/表级缓存策略的集合：
    * [`.show tables details`](show-tables-command.md)
    * [`.show database details`](../management/show-databases.md)
* 可以使用 [`.show follower database`](#show-follower-database) 查看在进行更改后的随从数据库上的替代设置

**语法**

`.alter` `follower` `database` *DatabaseName* `caching-policies-modification-kind` = (`none` | `union` | `replace`)

**示例**

```kusto
.alter follower database MyDB caching-policies-modification-kind = union
```

### <a name="alter-follower-database-prefetch-extents"></a>.alter follower database prefetch-extents

随从群集可以等待新数据从底层存储提取到到节点的 SSD（缓存），然后再使该数据可供查询。

以下命令会在每次进行架构刷新时更改预提取新区的随从数据库配置。 此命令需要 [DatabaseAdmin 权限](../management/access-control/role-based-authorization.md)。

> [!WARNING]
> * 此设置可能会降低随从数据库中数据的时效性。
> * 默认配置为 `false`，建议使用默认配置。
> * 选择将设置更改为 `true` 时，请严谨地评估配置更改后一段时间内对时效性的影响。

**语法**

`.alter` `follower` `database` *DatabaseName* `prefetch-extents` = (`true` | `false`)

**示例**

<!-- csl -->
```
.alter follower database MyDB prefetch-extents = false
```

## <a name="table-level-commands"></a>表级命令

### <a name="alter-follower-table-policy-caching"></a>.alter follower table policy caching

更改随从数据库上的表级缓存策略，以替代在先导群集中的源数据库上设置的策略。
它需要 [DatabaseAdmin 权限](../management/access-control/role-based-authorization.md)。 

**说明**

* 可以使用以下 `.show` 命令查看策略或查看在更改后生效的策略：
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* 可以使用 [`.show follower database`](#show-follower-database) 查看在进行更改后的随从数据库上的替代设置

**语法**

`.alter` `follower` `database` *DatabaseName* table *TableName* `policy` `caching` `hot` `=` *HotDataSpan*

`.alter` `follower` `database` *DatabaseName* tables `(`*TableName1*`,`...`,`*TableNameN*`)` `policy` `caching` `hot` `=` *HotDataSpan*

**示例**

```kusto
.alter follower database MyDb tables (Table1, Table2) policy caching hot = 7d
```

### <a name="delete-follower-table-policy-caching"></a>.delete follower table policy caching

删除随从数据库上的替代表级缓存策略，使在先导群集中的源数据库上设置的策略成为生效的策略。 需要 [DatabaseAdmin 权限](../management/access-control/role-based-authorization.md)。 

**说明**

* 可以使用以下 `.show` 命令查看策略或查看在更改后生效的策略：
    * [`.show database policy retention`](../management/retention-policy.md#show-retention-policy)
    * [`.show database details`](../management/show-databases.md)
    * [`.show table details`](show-tables-command.md)
* 可以使用 [`.show follower database`](#show-follower-database) 查看在进行更改后的随从数据库上的替代设置

**语法**

`.delete` `follower` `database` *DatabaseName* `table` *TableName* `policy` `caching`

`.delete` `follower` `database` *DatabaseName* `tables` `(`*TableName1*`,`...`,`*TableNameN*`)` `policy` `caching`

**示例**

```kusto
.delete follower database MyDB tables (Table1, Table2) policy caching
```

## <a name="sample-configuration"></a>示例配置

下面是用于配置随从数据库的示例步骤。

在本示例中：

* 我们的随从群集 `MyFollowerCluster` 将追随先导群集 `MyLeaderCluster` 中的数据库 `MyDatabase`。
    * `MyDatabase` 具有 `N` 表：`MyTable1`、`MyTable2`、`MyTable3`、... `MyTableN` (`N` > 3)。
    * 在 `MyLeaderCluster`上：
    
    | `MyTable1` 缓存策略 | `MyTable2` 缓存策略 | `MyTable3`...`MyTableN` 缓存策略   | `MyDatabase` 经授权的主体                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | 热数据跨度 = `7d`      | 热数据跨度 = `30d`     | 热数据跨度 = `365d`                   | *查看者* = `aadgroup=scubadivers@contoso.com`；*管理员* = `aaduser=jack@contoso.com` |
     
    * 在 `MyFollowerCluster` 上，我们需要：
    
    | `MyTable1` 缓存策略 | `MyTable2` 缓存策略 | `MyTable3`...`MyTableN` 缓存策略   | `MyDatabase` 经授权的主体                                                    |
    |---------------------------|---------------------------|------------------------------------------|---------------------------------------------------------------------------------------|
    | 热数据跨度 = `1d`      | 热数据跨度 = `3d`      | 热数据跨度 = `0d`（不缓存任何内容） | *管理员* = `aaduser=jack@contoso.com`、*查看者* = `aaduser=jill@contoso.com`         |

> [!IMPORTANT] 
> `MyFollowerCluster` 和 `MyLeaderCluster` 必须位于同一区域中。

### <a name="steps-to-execute"></a>要执行的步骤

先决条件：将群集 `MyFollowerCluster` 设置为追随群集 `MyLeaderCluster` 中的数据库 `MyDatabase`。

> [!NOTE]
> 运行控制命令的主体应为数据库 `MyDatabase` 上的 `DatabaseAdmin`。

#### <a name="show-the-current-configuration"></a>显示当前配置

查看追随 `MyFollowerCluster` 上的 `MyDatabase` 时所遵循的当前配置：

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| 列                              | Value                                                    |
|-------------------------------------|----------------------------------------------------------|
|DatabaseName                         | MyDatabase                                               |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.chinacloudapi.cn/cluster` |
|CachingPolicyOverride                | Null                                                     |
|AuthorizedPrincipalsOverride         | []                                                       |
|AuthorizedPrincipalsModificationKind | 无                                                     |
|IsAutoPrefetchEnabled                | False                                                    |
|TableMetadataOverrides               |                                                          |
|CachingPoliciesModificationKind      | Union                                                    |                                                                                                                      |

#### <a name="override-authorized-principals"></a>替代经授权的主体

将 `MyFollowerCluster` 上的 `MyDatabase` 的经授权主体集合替换为仅包含一个 AAD 用户作为数据库管理员并包含一个 AAD 用户作为数据库查看者的集合：

```kusto
.add follower database MyDatabase admins ('aaduser=jack@contoso.com')

.add follower database MyDatabase viewers ('aaduser=jill@contoso.com')

.alter follower database MyDatabase principals-modification-kind = replace
```

只有这两个特定主体才有权访问 `MyFollowerCluster` 上的 `MyDatabase`

```kusto
.show database MyDatabase principals
```

| 角色                       | PrincipalType | PrincipalDisplayName                        | PrincipalObjectId                    | PrincipalFQN                                                                      | 注释 |
|----------------------------|---------------|---------------------------------------------|--------------------------------------|-----------------------------------------------------------------------------------|-------|
| MyDatabase 数据库管理员  | AAD 用户      | Jack Kusto    (upn: jack@contoso.com)       | 12345678-abcd-efef-1234-350bf486087b | aaduser=87654321-abcd-efef-1234-350bf486087b;55555555-4444-3333-2222-2d7cd011db47 |       |
| MyDatabase 数据库查看者 | AAD 用户      | Jill Kusto    (upn: jack@contoso.com)       | abcdefab-abcd-efef-1234-350bf486087b | aaduser=54321789-abcd-efef-1234-350bf486087b;55555555-4444-3333-2222-2d7cd011db47 |       |

```kusto
.show follower database MyDatabase
| mv-expand parse_json(AuthorizedPrincipalsOverride)
| project AuthorizedPrincipalsOverride.Principal.FullyQualifiedName
```

| AuthorizedPrincipalsOverride_Principal_FullyQualifiedName                         |
|-----------------------------------------------------------------------------------|
| aaduser=87654321-abcd-efef-1234-350bf486087b;55555555-4444-3333-2222-2d7cd011db47 |
| aaduser=54321789-abcd-efef-1234-350bf486087b;55555555-4444-3333-2222-2d7cd011db47 |

#### <a name="override-caching-policies"></a>替代缓存策略

通过以下方式替换 `MyFollowerCluster` 上的 `MyDatabase` 的数据库级和表级缓存策略的集合：将所有表设置为不缓存其数据，但两个特定表（`MyTable1` 和 `MyTable2`）例外，这两个表分别将其数据缓存 `1d` 和 `3d`：

```kusto
.alter follower database MyDatabase policy caching hot = 0d

.alter follower database MyDatabase table MyTable1 policy caching hot = 1d

.alter follower database MyDatabase table MyTable2 policy caching hot = 3d

.alter follower database MyDatabase caching-policies-modification-kind = replace
```

只有这两个特定的表缓存了数据，其余表的热数据期间为 `0d`：

```kusto
.show tables details
| summarize TableNames = make_list(TableName) by CachingPolicy
```
| CachingPolicy                                                                | TableNames                  |
|------------------------------------------------------------------------------|-----------------------------|
| {"DataHotSpan":{"Value":"1.00:00:00"},"IndexHotSpan":{"Value":"1.00:00:00"}} | ["MyTable1"]                |
| {"DataHotSpan":{"Value":"3.00:00:00"},"IndexHotSpan":{"Value":"3.00:00:00"}} | ["MyTable2"]                |
| {"DataHotSpan":{"Value":"0.00:00:00"},"IndexHotSpan":{"Value":"0.00:00:00"}} | ["MyTable3",...,"MyTableN"] |

```kusto
.show follower database MyDatabase
| mv-expand parse_json(TableMetadataOverrides)
| project TableMetadataOverrides
```

| TableMetadataOverrides                                                                                              |
|---------------------------------------------------------------------------------------------------------------------|
| {"MyTable1":{"CachingPolicyOverride":{"DataHotSpan":{"Value":"1.00:00:00"},"IndexHotSpan":{"Value":"1.00:00:00"}}}} |
| {"MyTable2":{"CachingPolicyOverride":{"DataHotSpan":{"Value":"3.00:00:00"},"IndexHotSpan":{"Value":"3.00:00:00"}}}} |

#### <a name="summary"></a>摘要

查看追随 `MyFollowerCluster` 上的 `MyDatabase` 时的当前配置：

```kusto
.show follower database MyDatabase
| evaluate narrow() // just for presentation purposes
```

| 列                              | Value                                                                                                                                                                           |
|-------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|DatabaseName                         | MyDatabase                                                                                                                                                                      |
|LeaderClusterMetadataPath            | `https://storageaccountname.blob.core.chinacloudapi.cn/cluster`                                                                                                                 |
|CachingPolicyOverride                | {"DataHotSpan":{"Value":"00:00:00"},"IndexHotSpan":{"Value":"00:00:00"}}                                                                                                        |
|AuthorizedPrincipalsOverride         | [{"Principal":{"FullyQualifiedName":"aaduser=87654321-abcd-efef-1234-350bf486087b",...},{"Principal":{"FullyQualifiedName":"aaduser=54321789-abcd-efef-1234-350bf486087b",...}] |
|AuthorizedPrincipalsModificationKind | Replace                                                                                                                                                                         |
|IsAutoPrefetchEnabled                | False                                                                                                                                                                           |
|TableMetadataOverrides               | {"MyTargetTable":{"CachingPolicyOverride":{"DataHotSpan":{"Value":"3.00:00:00"}...},"MySourceTable":{"CachingPolicyOverride":{"DataHotSpan":{"Value":"1.00:00:00"},...}}}       |
|CachingPoliciesModificationKind      | Replace                                                                                                                                                                         |
