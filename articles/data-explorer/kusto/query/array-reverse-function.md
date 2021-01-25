---
title: array_reverse() - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的 array_reverse()。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
origin.date: 11/09/2020
ms.date: 01/22/2021
ms.openlocfilehash: 763c88be9587e711c3920e15f6ad5ad76b2e55ee
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614885"
---
# <a name="array_reverse"></a>array_reverse()

反转动态数组中元素的顺序。

## <a name="syntax"></a>语法

`array_reverse(`array`)`

## <a name="arguments"></a>参数

*array*：要反转的输入数组。

## <a name="returns"></a>返回

一个数组，其中包含与输入数组完全相同的元素，但其顺序相反。

## <a name="example"></a>示例

<!-- csl: https://help.kusto.chinacloudapi.cn:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_reverse(arr)
```

|结果|
|---|
|["example","an","is","this"]|
