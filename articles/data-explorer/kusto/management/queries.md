---
title: 查询管理 - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍了 Azure 数据资源管理器中的查询管理。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 03/23/2020
ms.date: 01/22/2021
ms.openlocfilehash: 8735e076f6ef5880449c21484375bdeaabadd153
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611423"
---
# <a name="queries-management"></a>查询管理

## <a name="show-queries"></a>.show 查询

`.show` `queries` 命令返回已达到最终状态的查询列表，并且调用该命令的用户有权查看：


* [数据库管理员或数据库监视员](../management/access-control/role-based-authorization.md)可以查看在他们的数据库上调用的任何命令。
* 其他用户只能看到由他们调用的查询。

**语法**

`.show` `queries`

## <a name="show-running-queries"></a>.show 正在运行的查询

`.show` `running` `queries` 命令返回用户或另一个用户或所有用户当前正在执行的查询的列表。

**语法**

```kusto
.show running queries
```

* (1) 返回调用用户当前正在执行的查询（需要读取访问权限）。

## <a name="cancel-query"></a>.cancel 查询

`.cancel` `query` 命令会开始尽力尝试取消特定正在运行的查询。

* 集群管理员可以取消任何正在运行的查询。
* 数据库管理员可以取消对其具有管理员访问权限的数据库调用的任何正在运行的查询。
* 所有主体都可以取消他们启动的正在运行的查询。

**语法**

`.cancel` `query` *ClientRequestId* [`with` `(` `reason` `=` *ReasonPhrase* `)`]

* ClientRequestId 是正在运行的查询的 `ClientRequestId` 属性的值，作为 `string` 文本。

* *ReasonPhrase*：如果已指定，则为描述取消正在运行的查询的原因的 `string` 文本。 如果查询成功取消，则此信息将包含在查询结果中。

**示例**

```kusto
.cancel query "KE.RunQuery;8f70e9ab-958f-4955-99df-d2a288b32b2c"
```
