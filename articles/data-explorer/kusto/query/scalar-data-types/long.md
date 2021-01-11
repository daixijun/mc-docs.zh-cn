---
title: long 数据类型 - Azure 数据资源管理器 | Microsoft Docs
description: 本文介绍 Azure 数据资源管理器中的 long 数据类型。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
origin.date: 10/23/2018
ms.date: 07/31/2020
ms.openlocfilehash: cdd4e8bd4ca56d9dcb4f3f88692f7075cde8b178
ms.sourcegitcommit: 4e1bc2e9b2a12dbcc05c52db5dbd1ae290aeb18d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2020
ms.locfileid: "97531829"
---
# <a name="the-long-data-type"></a>long 数据类型

`long` 数据类型表示一个 64 位宽的带符号整数。

## <a name="long-literals"></a>long 文本

可以使用以下语法指定 `long` 数据类型的文本：

`long` `(` *值* `)`

其中，Value 可以采用以下形式：
* 一个或多个数字，在这种情况下，文本值是这些数字的十进制表示形式。 例如，`long(12)` 是类型 `long` 的数字十二。
* 前缀 `0x` 后跟一个或多个十六进制数字。 例如，`long(0xf)` 等效于 `long(15)`。
* 减号 (`-`) 后跟一个或多个数字。 例如，`long(-1)` 是数字减去类型 `long` 之一。
* `null`，在这种情况下，这是 `long` 数据类型的 [Null 值](null-values.md)。 因此，类型 `long` 的 null 值为 `long(null)`。

Kusto 还仅对前两种形式支持类型 `long` 的没有前缀 `long(`/`)` 的文本。 因此，`123` 是 `long` 类型的文本，`0x123` 也是，但 `-2` 不是文本（目前被解释为应用于 long 类型的文本 `2` 的一元函数 `-`）。
 
有关将 long 转换为十六进制字符串的内容，请参阅 [ tohex() 函数](../tohexfunction.md)。