---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
origin.date: 03/27/2019
ms.date: 01/18/2021
ms.author: v-tawe
ms.custom: include file
ms.openlocfilehash: 49426234d709d980647908ef9286436734b95437
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231202"
---
可以访问在容器中生成的控制台日志。

首先，请运行以下命令，以便启用容器日志记录功能：

```azurecli
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

将 `<app-name>` 和 `<resource-group-name>` 替换为适合 Web 应用的名称。

启用容器日志记录功能以后，请运行以下命令来查看日志流：

```azurecli
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

如果没有立即看到控制台日志，请在 30 秒后重新查看。

若要随时停止日志流式处理，可键入 Ctrl+C 。

也可通过浏览器在 `https://<app-name>.scm.chinacloudsites.cn/api/logs/docker` 中检查日志文件。
