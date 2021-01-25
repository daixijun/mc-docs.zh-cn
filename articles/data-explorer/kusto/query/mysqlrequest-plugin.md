---
title: mysql_request 插件 - Azure 数据资源管理器
description: 本文介绍了 Azure 数据资源管理器中的 mysql_request 插件。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 02/24/2020
ms.date: 01/22/2021
zone_pivot_group_filename: zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: bdebc03aa77d9424c67baa85ba6db0eba6f04215
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614903"
---
# <a name="mysql_request-plugin-preview"></a>mysql_request 插件（预览）

::: zone pivot="azuredataexplorer"

`mysql_request` 插件将 SQL 查询发送到 MySQL Server 网络终结点，并返回结果中的第一个行集。 此查询可能返回多个行集，但只有第一个行集可用于 Kusto 查询的其余部分。

> [!IMPORTANT]
> `mysql_request` 插件处于预览模式，默认为禁用。
> 若要启用此插件，请运行 [`.enable plugin mysql_request` 命令](../management/enable-plugin.md)。 若要查看启用了哪些插件，请使用 [`.show plugin` 管理命令](../management/show-plugins.md)。

## <a name="syntax"></a>语法

`evaluate` `mysql_request` `(` *ConnectionString* `,` *SqlQuery* [`,` *SqlParameters*] `)`

## <a name="arguments"></a>参数

名称 | 类型 | 说明 | 必需/可选 |
---|---|---|---
| *ConnectionString* | `string` literal | 表示指向 MySQL Server 网络终结点的连接字符串。 请了解[身份验证](#username-and-password-authentication)以及指定[网络终结点](#specify-the-network-endpoint)的方法。 | 必须 |
| *SqlQuery* | `string` literal | 表示要对 SQL 终结点执行的查询。 必须返回一个或多个行集，但只有第一个行集可用于 Kusto 查询的其余部分。 | 必须|
| SqlParameters | 类型为 `dynamic` 的常量值 | 保存作为参数随查询传递的键值对。 | 可选 |

## <a name="set-callout-policy"></a>设置标注策略

该插件会向 MySql DB 进行标注。 请确保群集的[标注策略](../management/calloutpolicy.md)允许对目标 MySqlDbUri 进行 `mysql` 类型的调用。

以下示例演示如何为 MySQL DB 定义标注策略。 建议将标注策略限制到特定的终结点（`my_endpoint1`、`my_endpoint2`）。

```kusto
[
  {
    "CalloutType": "mysql",
    "CalloutUriRegex": "my_endpoint1\\.mysql\\.database\\.azure\\.com",
    "CanCall": true
  },
  {
    "CalloutType": "mysql",
    "CalloutUriRegex": "my_endpoint2\\.mysql\\.database\\.azure\\.com",
    "CanCall": true
  }
]
```

以下示例显示了一条针对 `mysql` CalloutType 修改标注策略的命令：

```kusto
.alter cluster policy callout @'[{"CalloutType": "mysql", "CalloutUriRegex": "\\.mysql\\.database\\.azure\\.com", "CanCall": true}]'
```

## <a name="username-and-password-authentication"></a>用户名和密码身份验证

mysql_request 插件仅支持向 MySQL Server 终结点进行用户名和密码身份验证，未与 Azure Active Directory 身份验证集成。 

用户名和密码是通过以下参数，作为连接字符串的一部分提供的：

`User ID=...; Password=...;`
    
> [!WARNING]
> 应该对连接字符串和查询中的机密信息或受保护信息进行模糊处理，使其在任何 Kusto 跟踪中都被忽略。 有关详细信息，请参阅[经过模糊处理的字符串文本](scalar-data-types/string.md#obfuscated-string-literals)。

## <a name="encryption-and-server-validation"></a>加密和服务器验证

出于安全方面的原因，连接到 MySQL Server 网络终结点时，`SslMode` 会无条件地设置为 `Required`。 因此，必须为 SQL Server 配置有效的 SSL/TLS 服务器证书。

## <a name="specify-the-network-endpoint"></a>指定网络终结点

将 SQL 网络终结点指定为连接字符串的一部分。

**语法**：

`Server` `=` *FQDN* [`Port` `=` *Port*]

其中：

* *FQDN* 是终结点的完全限定域名。
* *Port* 是终结点的 TCP 端口。 默认为 `3306`。

## <a name="examples"></a>示例


### <a name="sql-query-to-azure-mysql-db"></a>向 Azure MySQL DB 进行的 SQL 查询

下面的示例向 Azure MySQL DB 数据库发送 SQL 查询。 它从 `[dbo].[Table]` 检索所有记录，然后处理结果。

> [!NOTE]
> 不应将此示例作为以这种方式筛选或投影数据的建议。 SQL 查询应当构造为尽可能返回最小的数据集，因为 Kusto 优化器当前不会尝试优化 Kusto 与 SQL 之间的查询。

```kusto
evaluate sql_request(
    'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

### <a name="sql-authentication-with-username-and-password"></a>使用用户名和密码进行的 SQL 身份验证

下面的示例与上一个示例相同，但 SQL 身份验证是通过使用用户名和密码来执行的。 考虑到保密性，我们使用经过模糊处理的字符串。

```kusto
evaluate sql_request(
   'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select * from [dbo].[Table]')
| where Id > 0
| project Name
```

### <a name="sql-query-to-azure-sql-db-with-modifications"></a>向 Azure SQL DB 进行的 SQL 查询（有修改）

下面的示例向 Azure SQL DB 数据库发送一个 SQL 查询，从 `[dbo].[Table]` 检索所有记录，同时追加另一个 `datetime` 列，然后在 Kusto 端处理结果。
它指定要在 SQL 查询中使用的 SQL 参数 (`@param0`)。

```kusto
evaluate mysql_request(
  'Server=contoso.mysql.database.azure.com; Port = 3306;'
    'Database=Fabrikam;'
    h'UID=USERNAME;'
    h'Pwd=PASSWORD;', 
  'select *, @param0 as dt from [dbo].[Table]',
  dynamic({'param0': datetime(2020-01-01 16:47:26.7423305)}))
| where Id > 0
| project Name
```

::: zone-end

::: zone pivot="azuremonitor"

Azure Monitor 不支持此功能

::: zone-end
