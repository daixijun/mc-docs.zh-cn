---
title: isnotnull() - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍 Azure 数据资源管理器中的 isnotnull()。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 02/13/2020
ms.date: 01/22/2021
ms.openlocfilehash: b0d0e09e59d0c0147faed1a214863d5da4e707f2
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611622"
---
# <a name="isnotnull"></a>isnotnull()

如果参数不为 null，则返回 `true`。

## <a name="syntax"></a>语法

`isnotnull(`[*value*]`)`

`notnull(`[*value*]`)` - `isnotnull` 的别名

## <a name="example"></a>示例

```kusto
T | where isnotnull(PossiblyNull) | count
```
