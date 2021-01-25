---
title: count 运算符 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 count 运算符。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 04/16/2020
ms.date: 01/22/2021
ms.openlocfilehash: ceac5b458aa6e8140704456dcaa485677b4b8b4b
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611507"
---
# <a name="count-operator"></a>count 运算符

返回输入记录集中的记录数。

## <a name="syntax"></a>语法

`T | count`

## <a name="arguments"></a>参数

*T*：待计算记录数的表格数据。

## <a name="returns"></a>返回

此函数返回包含单个记录且列类型为 `long` 的表。 唯一单元格的值是 *T* 中的记录数。 

## <a name="example"></a>示例

<!-- csl: https://help.kusto.chinacloudapi.cn/Samples -->
```kusto
StormEvents | count
```

## <a name="see-also"></a>另请参阅

有关 count() 聚合函数的信息，请参阅 [count()（聚合函数）](count-aggfunction.md)。
