---
title: .create table based-on - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 `.create table based-on` 命令
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: mispecto
ms.service: data-explorer
ms.topic: reference
origin.date: 12/29/2020
ms.date: 01/22/2021
ms.openlocfilehash: 5861ce9fd0aaa63bf73a02ed518d763c645f828f
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614911"
---
# <a name="create-table-based-on"></a>.create table based-on

基于现有表创建新的空表。 此命令必须在特定数据库的上下文中运行。

需要[数据库管理员权限](access-control/role-based-authorization.md)。

> [!NOTE]
> 源表的所有属性都将复制到新表中，但以下情况除外：
> * [更新策略](updatepolicy.md)
> * [授权主体](security-roles.md#managing-table-security-roles)：使用 `.create table based-on` 命令时，当前主体将添加到表管理员。

## <a name="syntax"></a>语法

`.create` `table` *TableName* `based-on` *OtherTable*  [`with` `(`[`docstring` `=` *Documentation*] [`,` `folder` `=` *FolderName*] `)`]

## <a name="arguments"></a>参数

* TableName：新表名称
* OtherTable：源表名称
* 文档：替代默认表文档字符串
* FolderName：替代目标文件夹名称

## <a name="returns"></a>返回

此命令以 JSON 格式返回新表的架构，类似于运行以下命令：

```kusto
.show table MyLogs schema as json
```

## <a name="example"></a>示例

```kusto
.create table MyLogs_Temp based-on MyLogs with (folder="TempTables")
```

