---
title: hll_merge()（聚合函数）- Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍 Azure 数据资源管理器中的 hll_merge()（聚合函数）。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 04/15/2019
ms.date: 01/22/2021
ms.openlocfilehash: 6b2aa044a2db6617d000bc428c0613ed88bc35bf
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611549"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge()（聚合函数）

将组中的 `HLL` 结果合并为单个 `HLL` 值。

* 只能在 [summarize](summarizeoperator.md) 内的聚合上下文中使用。

有关详细信息，请参阅[基础算法 (HyperLogLog  ) 和估算准确度](dcount-aggfunction.md#estimation-accuracy)。

## <a name="syntax"></a>语法

`summarize` `hll_merge(`*Expr*`)`

## <a name="arguments"></a>参数

* `*Expr*`：将要用于聚合计算的表达式。

## <a name="returns"></a>返回

此函数返回组中 `*Expr*` 的合并 `hll` 值。
 
**提示**

1) 使用函数 [dcount_hll](dcount-hllfunction.md) 根据 `hll` / `hll-merge` 聚合函数计算 `dcount`。
