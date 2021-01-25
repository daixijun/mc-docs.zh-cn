---
title: 禁用插件命令 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的插件管理命令 .disable plugin。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 11/02/2020
ms.date: 01/22/2021
ms.openlocfilehash: 1fa8ef31d54388d2c58974249ca3fb0061a4c4d6
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614881"
---
# <a name="disable-plugin"></a>.disable plugin

禁用插件。

此命令需要 `All Databases admin` 权限。

## <a name="syntax"></a>语法

`.disable` `plugin` *PluginName*

## <a name="example"></a>示例
 
<!-- csl -->
```kusto
.disable plugin autocluster
``` 

## <a name="next-steps"></a>后续步骤

* [`.show plugins`](show-plugins.md)
* [`.enable plugin`](enable-plugin.md)

