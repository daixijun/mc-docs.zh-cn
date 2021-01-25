---
title: .create function - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍 Azure 数据资源管理器中的 .create function。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 01/22/2021
ms.openlocfilehash: b01e667c6c5ea2ef2a3338e44146e09635c40a40
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611652"
---
# <a name="create-function"></a>.create function

创建存储函数，该函数是具有给定名称的可重用 [`let` 语句](../query/letstatement.md)函数。 函数定义与数据库元数据一起保留。

函数可以调用其他函数（不支持递归性），并且允许 `let` 语句作为函数正文的一部分。 请参阅 [`let` 语句](../query/letstatement.md)。

参数类型和 CSL 语句的规则与 [`let` 语句](../query/letstatement.md)的规则相同。
    
**语法**

`.create` `function` [`ifnotexists`] [`with` `(`[`docstring` `=` *Documentation*] [`,` `folder` `=` *FolderName*] `)`] [`,` `skipvalidation` `=` 'true'] `)`] *FunctionName* `(` *ParamName* `:` *ParamType* [`,` ...] `)` `{` *FunctionBody* `}`

**输出**    
    
|输出参数 |类型 |说明
|---|---|--- 
|名称  |String |函数的名称。 
|parameters  |String |函数所需的参数。
|正文  |String |（零个或多个）`let` 语句，后跟有效的 CSL 表达式，该表达式在函数调用时求值。
|文件夹|String|用于 UI 函数分类的文件夹。 此参数不会更改调用函数的方式。
|DocString|String|用于 UI 目的的函数说明。

> [!NOTE]
> * 如果函数已存在：
>    * 如果指定了 `ifnotexists` 标志，则该命令将被忽略（不应用更改）。
>    * 如果未指定 `ifnotexists` 标志，则将返回错误。
>    * 若要更改现有函数，请参阅 [`.alter function`](alter-function.md)
> * 需要[数据库用户权限](../management/access-control/role-based-authorization.md)。
> * `let` 语句并非支持所有数据类型。 支持的类型为：布尔值、字符串、长整型、日期/时间、时间跨度、双精度型和动态型。
> * 使用“skipvalidation”跳过函数的语义验证。 如果以不正确的顺序创建函数，并且之前创建了使用 F2 的 F1，这会很有用。

**示例** 

```kusto
.create function 
with (docstring = 'Simple demo function', folder='Demo')
MyFunction1()  {StormEvents | limit 100}
```

|名称|parameters|正文|文件夹|DocString|
|---|---|---|---|---|
|MyFunction1|()|{StormEvents &#124; limit 100}|演示|简单演示函数|

```kusto
.create function
with (docstring = 'Demo function with parameter', folder='Demo')
 MyFunction2(myLimit: long)  {StormEvents | limit myLimit}
```

|名称|parameters|正文|文件夹|DocString|
|---|---|---|---|---|
|MyFunction2|(myLimit:long)|{StormEvents &#124; limit myLimit}|演示|带参数的演示函数|
