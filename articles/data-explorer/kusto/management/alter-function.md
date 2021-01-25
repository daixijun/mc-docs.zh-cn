---
title: .alter 函数 - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍了 Azure 数据资源管理器中的 .alter 函数。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/11/2020
ms.date: 01/22/2021
ms.openlocfilehash: ca3a6e02d14ba32fa9c93db38679100ced9c3747
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611716"
---
# <a name="alter-function"></a>.alter function

更改现有函数并将其存储在数据库元数据中。
参数类型和 CSL 语句的规则与 [`let` 语句](../query/letstatement.md)的规则相同。

**语法**

```kusto
.alter function [with (docstring = '<description>', folder='<name>', skipvalidation='true')] [FunctionName] ([paramName:paramType], ...) { CSL-statement }
```
    
|输出参数 |类型 |说明
|---|---|--- 
|名称  |String |函数的名称。
|parameters  |String |函数所需的参数。
|正文  |String |（零个或多个）`let` 语句，后跟有效的 CSL 表达式，该表达式在函数调用时求值。
|文件夹|String|用于 UI 函数分类的文件夹。 此参数不会更改调用函数的方式。
|DocString|String|用于 UI 目的的函数说明。

> [!NOTE]
> * 如果该函数不存在，将返回错误。 若要创建新函数，请参阅 [`.create function`](create-function.md)
> * 需要[数据库管理员权限](../management/access-control/role-based-authorization.md)
> * 允许最初创建该函数的[数据库用户](../management/access-control/role-based-authorization.md)修改函数。 
> * `let` 语句并不支持所有的 Kusto 类型。 支持的类型为：字符串、长整型、日期时间、时间跨度和双精度型。
> * 使用 `skipvalidation` 跳过函数的语义验证。 如果以不正确的顺序创建函数，并且之前创建了使用 F2 的 F1，这会很有用。
 
**示例** 

```kusto
.alter function
with (docstring = 'Demo function with parameter', folder='MyFolder')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
``` 
    
|名称 |parameters |正文|文件夹|DocString
|---|---|---|---|---
|MyFunction2 |(myLimit: long)| {StormEvents &#124; limit myLimit}|MyFolder|带参数的演示函数|
