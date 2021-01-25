---
title: 启用插件命令 - Azure 数据资源管理器
description: 本文介绍 Azure 数据资源管理器中的插件管理命令 .enable plugin。
services: data-explorer
author: orspod
ms.author: v-tawe
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
origin.date: 11/02/2020
ms.date: 01/22/2021
ms.openlocfilehash: c3ef697fa774cdc0b90b3aafd991a41456e954b1
ms.sourcegitcommit: 7be0e8a387d09d0ee07bbb57f05362a6a3c7b7bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98614913"
---
# <a name="enable-plugin"></a>.enable plugin

启用插件。

此命令需要 `All Databases admin` 权限。

## <a name="syntax"></a>语法

`.enable` `plugin` *PluginName*

## <a name="example"></a>示例

<!-- csl -->
```kusto
.enable plugin autocluster
``` 

## <a name="next-steps"></a>后续步骤

* [`.disable plugin`](disable-plugin.md)
* [`.show plugins`](show-plugins.md)

