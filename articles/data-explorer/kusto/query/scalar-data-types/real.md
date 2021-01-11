---
title: real 数据类型 - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍 Azure 数据资源管理器中的 real 数据类型。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 10/23/2018
ms.date: 07/31/2020
ms.openlocfilehash: 091247979fde4ab3741153167c70a1e2401233eb
ms.sourcegitcommit: 4e1bc2e9b2a12dbcc05c52db5dbd1ae290aeb18d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2020
ms.locfileid: "97532010"
---
# <a name="the-real-data-type"></a>real 数据类型

`real` 数据类型表示 64 位宽的双精度浮点数。

`real` 数据类型的文本与 .NET 的 `System.Double` 具有相同的表示形式。 `1.0`、`0.1`、`1e5` 均为 `real` 类型的文本。

有几种特殊文本形式：
* `real(null)`：这是 [NULL 值](null-values.md)。
* `real(nan)`：非数值 (NaN)。 例如，`0.0` 除以另一个 `0.0` 的结果。
* `real(+inf)`：正无穷大。 例如，`1.0` 除以 `0.0` 的结果。
* `real(-inf)`：负无穷大。 例如，`-1.0` 除以 `0.0` 的结果。