---
title: 插件命令 show plugins - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的插件管理命令。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 11/02/2020
ms.date: 01/22/2021
ms.openlocfilehash: 6c4a088c11442ab093bd96c94448d9101e2d3db2
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614891"
---
# <a name="show-plugins"></a>.show plugins


列出群集的所有插件。

## <a name="syntax"></a>语法

`.show` `plugins`

## <a name="output"></a>输出

返回一个表，其中包含以下字段：
* **PluginName**：插件的名称
* **IsEnabled**：一个布尔值，指示插件是否已启用

## <a name="example"></a>示例

<!-- csl -->
```kusto
.show plugins
``` 

| PluginName | IsEnabled |
|---|---|
| autocluster | false |
| basket      | 是  |

## <a name="next-steps"></a>后续步骤

* [.disable plugin](disable-plugin.md)
* [.enable plugin](enable-plugin.md)
