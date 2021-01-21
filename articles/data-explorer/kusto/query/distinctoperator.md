---
title: distinct 运算符 - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍 Azure 数据资源管理器中的 distinct 运算符。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 01/22/2021
ms.localizationpriority: high
ms.openlocfilehash: 18838857f477d22c4bb9484c8b538a996304a6c6
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611465"
---
# <a name="distinct-operator"></a>distinct 运算符

生成一个表，其中包含输入表中所提供列的不同组合。 

```kusto
T | distinct Column1, Column2
```

生成一个表，其中包含输入表中所有列的不同组合。

```kusto
T | distinct *
```

## <a name="example"></a>示例

显示水果与价格的不同组合。

```kusto
Table | distinct fruit, price
```

:::image type="content" source="images/distinctoperator/distinct.PNG" alt-text="两个表。一个表包含供应商、水果类型和价格，其中一些水果价格组合重复出现。第二个表仅列出独特的组合。":::

**备注**

* 与 `summarize by ...` 不同，`distinct` 运算符支持提供星号 (`*`) 作为组键，使其更易于用于宽表。
* 如果分组依据键的基数很高，则可将 `summarize by ...` 与[随机策略](shufflequery.md)一起使用。