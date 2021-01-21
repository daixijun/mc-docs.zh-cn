---
title: Kusto 外部表常规控制命令 - Azure 数据资源管理器
description: 本文介绍常规的外部表控制命令
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 05/26/2020
ms.date: 01/22/2021
ms.openlocfilehash: 123913a2fa3dbc01548c3b0bc8ccbd51d39adf16
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611304"
---
# <a name="external-table-general-control-commands"></a>外部表常规控制命令

有关外部表的概述，请参阅[外部表](../query/schema-entities/externaltables.md)。 以下命令与任何外部表（任何类型）相关。

## <a name="show-external-tables"></a>.show external tables

* 返回数据库中的所有外部表（或特定外部表）。
* 需要[数据库监视者权限](../management/access-control/role-based-authorization.md)。

**语法：** 

`.show` `external` `tables`

`.show` `external` `table` *TableName*

**输出**

| 输出参数 | 类型   | 说明                                                         |
|------------------|--------|---------------------------------------------------------------------|
| TableName        | string | 外部表的名称                                             |
| TableType        | string | 外部表的类型                                              |
| 文件夹           | string | 表的文件夹                                                     |
| DocString        | string | 用来记录表的字符串                                       |
| 属性       | string | 表的 JSON 序列化属性（特定于表的类型） |


**示例：**

```kusto
.show external tables
.show external table T
```

| TableName | TableType | 文件夹         | DocString | 属性 |
|-----------|-----------|----------------|-----------|------------|
| T         | Blob      | ExternalTables | Docs      | {}         |


## <a name="show-external-table-schema"></a>.show external table schema

* 以 JSON 或 CSL 形式返回外部表的架构。 
* 需要[数据库监视者权限](../management/access-control/role-based-authorization.md)。

**语法：** 

`.show` `external` `table` *TableName* `schema` `as` (`json` | `csl`)

`.show` `external` `table` *TableName* `cslschema`

**输出**

| 输出参数 | 类型   | 说明                        |
|------------------|--------|------------------------------------|
| TableName        | string | 外部表的名称            |
| 架构           | string | JSON 格式的表架构 |
| DatabaseName     | string | 表的数据库名称             |
| 文件夹           | string | 表的文件夹                    |
| DocString        | string | 用来记录表的字符串      |

**示例：**

```kusto
.show external table T schema as JSON
```

```kusto
.show external table T schema as CSL
.show external table T cslschema
```

**输出：**

*json：*

| TableName | 架构    | DatabaseName | 文件夹         | DocString |
|-----------|----------------------------------|--------------|----------------|-----------|
| T         | {"Name":"ExternalBlob",<br>"Folder":"ExternalTables",<br>"DocString":"Docs",<br>"OrderedColumns":[{"Name":"x","Type":"System.Int64","CslType":"long","DocString":""},{"Name":"s","Type":"System.String","CslType":"string","DocString":""}]} | DB           | ExternalTables | Docs      |


csl：

| TableName | 架构          | DatabaseName | 文件夹         | DocString |
|-----------|-----------------|--------------|----------------|-----------|
| T         | x:long,s:string | DB           | ExternalTables | Docs      |

## <a name="drop-external-table"></a>.drop external table

* 删除外部表 
* 无法在此操作后还原外部表定义
* 需要[数据库管理员权限](../management/access-control/role-based-authorization.md)。

**语法：**  

`.drop` `external` `table` *TableName* [`ifexists`]

**输出**

返回已删除表的属性。 有关详细信息，请参阅 [`.show external tables`](#show-external-tables)。

**示例：**

```kusto
.drop external table ExternalBlob
```

| TableName | TableType | 文件夹         | DocString | 架构       | 属性 |
|-----------|-----------|----------------|-----------|-----------------------------------------------------|------------|
| T         | Blob      | ExternalTables | Docs      | [{ "Name": "x",  "CslType": "long"},<br> { "Name": "s",  "CslType": "string" }] | {}         |

## <a name="next-steps"></a>后续步骤

* [在 Azure 存储或 Azure Data Lake 中创建和更改外部表](external-tables-azurestorage-azuredatalake.md)
* [创建和更改外部 SQL 表](external-sql-tables.md)
