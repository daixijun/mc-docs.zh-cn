---
title: .alter function docstring - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 .alter function docstring。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/11/2020
ms.date: 01/22/2021
ms.openlocfilehash: 4bc6d9a0e8e93b21fe0ec431ec47850862739e24
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611420"
---
# <a name="alter-function-docstring"></a>.alter function docstring

更改现有函数的 `DocString` 值。

`.alter` `function` *FunctionName* `docstring` *Documentation*

> [!NOTE]
> * 需要[数据库管理员权限](../management/access-control/role-based-authorization.md)
> * 允许最初创建函数的[数据库用户](../management/access-control/role-based-authorization.md)修改该函数。
> * 如果该函数不存在，则返回错误。 有关如何创建新函数的详细信息，请参阅 [`.create function`](create-function.md)。

|输出参数 |类型 |说明
|---|---|--- 
|名称  |String |函数的名称
|parameters  |String |函数所需的参数
|正文  |String |（零个或零个以上）`let` 语句，后跟在调用函数时计算的有效 CSL 表达式
|文件夹|String|用于 UI 函数分类的文件夹。 此参数不会更改函数调用方式
|DocString|String|用于 UI 目的的函数的说明

**示例** 

```kusto
.alter function MyFunction1 docstring "Updated docstring"
```
    
|名称 |parameters |正文|文件夹|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| {StormEvents &#124; limit myLimit}|MyFolder|已更新 docstring|
