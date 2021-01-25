---
title: .show 数据库架构 - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍 Azure 数据资源管理器中的 .show 数据库架构。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 01/22/2021
ms.openlocfilehash: f4df0bf5daaf187bf0c830f56b0065e0d44ee2b8
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611659"
---
# <a name="show-database-schema-commands"></a>.show database schema 命令

以下命令将数据库架构显示为表、JSON 对象或 CSL 脚本。

## <a name="show-databases-schema"></a>.show 数据库架构

**语法**

`.show` `database` *DatabaseName* `schema` [`details`] [`if_later_than` *"Version"* ] 

`.show` `databases` `(` *DatabaseName1*`,` ...`)` `schema` `details` 
 
`.show` `databases` `(` *DatabaseName1* if_later_than *"Version"* `,` ...`)` `schema` `details`

**返回**

返回所选数据库的结构的简单列表，其所有表和列都在单个表或 JSON 对象中。
与版本一起使用时，只有在版本比提供的版本更高时才会返回数据库。

> [!NOTE]
> 该版本只能以“vMM.mm”格式提供。 MM 代表主版本，mm 代表次要版本。

**示例** 
 
数据库“TestDB”具有一个被称为“Events”的表。

```kusto
.show database TestDB schema 
```

**输出**

|DatabaseName|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName|版本
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v.1.1       
|TestDB|事件|||正确|False||       
|TestDB|事件| 名称|System.String|正确|False||     
|TestDB|事件| StartTime|  System.DateTime|正确|False||    
|TestDB|事件| EndTime|    System.DateTime|正确|False||        
|TestDB|事件| 城市|   System.String|正确| False||     
|TestDB|事件| SessionId|  System.Int32|True|  True|| 

**示例** 

在下面的示例中，该数据库只有在其版本比提供的版本高时才会被返回。
 
```kusto
.show database TestDB schema if_later_than "v1.0" 
```

**输出**

|DatabaseName|TableName|ColumnName|ColumnType|IsDefaultTable|IsDefaultColumn|PrettyName|版本
|---|---|---|---|---|---|---|--- 
|TestDB||||False|False||v.1.1       
|TestDB|事件|||正确|False||       
|TestDB|事件| 名称|System.String|正确|False||     
|TestDB|事件| StartTime|  System.DateTime|正确|False||    
|TestDB|事件| EndTime|    System.DateTime|正确|False||        
|TestDB|事件| 城市|   System.String|正确| False||     
|TestDB|事件| SessionId|  System.Int32|True|  True||  

因为提供的版本低于当前数据库的版本，所以返回了“TestDB”架构。 提供相同或更高的版本将生成空结果。

## <a name="show-database-schema-as-json"></a>.show database schema as json

**语法**

`.show` `database` *DatabaseName* `schema` [`if_later_than` *"Version"* ]  `as` `json`
 
`.show` `databases` `(` *DatabaseName1*`,` ...`)` `schema` `as` `json`
 
`.show` `databases` `(` *DatabaseName1* if_later_than *"Version"* `,` ...`)` `schema` `as` `json`

**返回**

返回所选数据库的结构的简单列表，这些数据库所有的表和列都作为 JSON 对象返回。
与版本一起使用时，只有在版本比提供的版本更高时才会返回数据库。

**示例** 
 
```kusto
.show database TestDB schema as json
```

**输出**

```json
"{""Databases"":{""TestDB"":{""Name"":""TestDB"",""Tables"":{""Events"":{""Name"":""Events"",""DefaultColumn"":null,""OrderedColumns"":[{""Name"":""Name"",""Type"":""System.String""},{""Name"":""StartTime"",""Type"":""System.DateTime""},{""Name"":""EndTime"",""Type"":""System.DateTime""},{""Name"":""City"",""Type"":""System.String""},{""Name"":""SessionId"",""Type"":""System.Int32""}]}},""PrettyName"":null,""MajorVersion"":1,""MinorVersion"":1,""Functions"":{}}}}"
```

## <a name="show-database-schema-as-csl-script"></a>.show database schema as csl script

生成包含所有必需命令的 CSL 脚本，以创建给定的（或当前的）数据库架构的副本。

**语法**

`.show` `database` *DatabaseName* `schema` `as` `csl` `script` [`with(`*Options*`)`]

**参数**

以下 Options 都是可选的：

* `IncludeEncodingPolicies`：(`true` | `false`) - 如果为 `true`，将会包括数据库/表/列级别的编码策略。 默认为 `false`。 
* `IncludeSecuritySettings`：(`true` | `false`) - 默认为 `false`。 如果为 `true`，则会包括以下选项：
  * 数据库/表级别的已授权主体。
  * 表级别的行级别安全策略。
  * 表级别的受限查看访问策略。
* `IncludeIngestionMappings`：(`true` | `false`) - 如果为 `true`，将会包括表级别的引入映射。 默认为 `false`。 

**返回**

该脚本会以字符串形式返回，其中会包含：

* 用于创建数据库中所有表的命令。
* 用于将所有数据库/表/列策略设置为与原始策略匹配的命令。
* 用于创建或更改数据库中所有用户定义的函数的命令。

**示例** 
 
```kusto
.show database TestDB schema as csl script

.show database TestDB schema as csl script with(IncludeSecuritySettings = true)
```
