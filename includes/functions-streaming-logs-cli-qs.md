---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/17/2020
ms.author: v-junlch
ms.openlocfilehash: 7ce91189091d5bed123c65d87ab09c966b059f57
ms.sourcegitcommit: 054636c134cc0f53c194a6b76668644e18d1c4fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95970679"
---
运行以下命令以查看准实时[流式处理日志](../articles/azure-functions/functions-run-local.md#enable-streaming-logs)：

```console
func azure functionapp logstream <APP_NAME> 
```

在单独的终端窗口或浏览器中，再次调用远程函数。 终端中显示了 Azure 中函数执行的详细日志。 

