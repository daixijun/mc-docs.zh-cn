---
title: take 运算符 - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍 Azure 数据资源管理器中的 take 运算符。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 01/22/2021
ms.openlocfilehash: ef0836c824f9404a46818946b781dcd27af63781
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611639"
---
# <a name="take-operator"></a>take 运算符

最多返回指定数量的行。

```kusto
T | take 5
```

除非对源数据进行排序，否则无法保证返回哪些记录。

> [!NOTE]
> `take` 是一种在以交互方式浏览数据时查看小样本记录的简单、快速且高效的方法，但请注意，即使数据集没有变化，在多次执行时，也不能保证其结果的一致性。
> 即使查询返回的行数不由查询显式限制（未使用 `take` 运算符），Kusto 也会默认限制该数字。 如需更多详细信息，请参阅 [Kusto 查询限制](../concepts/querylimits.md)。

## <a name="syntax"></a>语法

`take` *NumberOfRows*
`limit` *NumberOfRows*

（`take` 和 `limit` 是同义词。）

## <a name="paging-of-query-results"></a>查询结果分页

用于实现分页的方法包括：

* 将查询结果导出到外部存储，并以分页方式列出生成的数据。
* 编写中间层应用程序，该应用程序通过缓存 Kusto 查询的结果提供有状态分页 API。
* 在[存储的查询结果](../management/stored-query-results.md#pagination)中使用分页。


## <a name="see-also"></a>请参阅

* [sort 运算符](sortoperator.md)
* [top 运算符](topoperator.md)
* [top-nested 运算符](topnestedoperator.md)