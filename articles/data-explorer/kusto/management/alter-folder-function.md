---
title: .alter function folder - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍 Azure 数据资源管理器中的 .alter function folder。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/11/2020
ms.date: 01/22/2021
ms.openlocfilehash: 19ef3a6e55c8d03971a7a5a1b531b179f30dba26
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611717"
---
# <a name="alter-function-folder"></a>.alter function folder

更改现有函数的 Folder 值。

`.alter` `function` *FunctionName* `folder` *Folder*

> [!NOTE]
> * 需要[数据库管理员权限](../management/access-control/role-based-authorization.md)
> * 允许最初创建该函数的[数据库用户](../management/access-control/role-based-authorization.md)修改函数。 
> * 如果该函数不存在，将返回错误。 若要创建新函数，请参阅 [`.create function`](create-function.md)

|输出参数 |类型 |说明
|---|---|--- 
|名称  |String |函数的名称。 
|parameters  |String |函数所需的参数。
|正文  |String |（零个或多个）Let 语句，后跟有效的 CSL 表达式，该表达式在函数调用时求值。
|文件夹|String|用于 UI 函数分类的文件夹。 此参数不会更改调用函数的方式。
|DocString|String|用于 UI 目的的函数说明。

**示例** 

```kusto
.alter function MyFunction1 folder "Updated Folder"
```
    
|名称 |parameters |正文|文件夹|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| {StormEvents &#124; limit myLimit}|已更新的文件夹|一些 DocString|

```kusto
.alter function MyFunction1 folder @"First Level\Second Level"
```
    
|名称 |parameters |正文|文件夹|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| {StormEvents &#124; limit myLimit}|第一级\第二级|一些 DocString|