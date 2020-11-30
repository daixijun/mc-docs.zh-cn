---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/17/2020
ms.author: v-junlch
ms.openlocfilehash: 658495bf6fe4c5ae0e7e517b79c841fa3998cf91
ms.sourcegitcommit: 054636c134cc0f53c194a6b76668644e18d1c4fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95970677"
---
## <a name="deploy-the-function-project-to-azure"></a>将函数项目部署到 Azure

在 Azure 中成功创建函数应用后，便可以使用 [func azure functionapp publish](../articles/azure-functions/functions-run-local.md#project-file-deployment) 命令部署本地函数项目。  

在以下示例中，请将 `<APP_NAME>` 替换为你的应用的名称。

```console
func azure functionapp publish <APP_NAME>
```

publish 命令显示类似于以下输出的结果（为简洁起见，示例中的结果已截断）：

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.chinacloudsites.cn/api/httpexample
</pre>

