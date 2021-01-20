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
ms.openlocfilehash: 8de4c771abd5daf6d9144190be18d0b6cc9a32eb
ms.sourcegitcommit: c8ec440978b4acdf1dd5b7fda30866872069e005
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231200"
---
若要访问应用服务中的应用程序代码内生成的控制台日志，请在 Azure CLI 中运行以下命令以打开诊断日志记录：

```azurecli
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

`--level` 的可能值为：`Error`、`Warning`、`Info` 和 `Verbose`。 每个后续级别包括上一个级别。 例如：`Error` 仅包含错误消息，`Verbose` 则包含所有消息。

启用诊断日志记录功能以后，请运行以下命令来查看日志流：

```azurecli
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

如果没有立即看到控制台日志，请在 30 秒后重新查看。

> [!NOTE]
> 也可通过浏览器在 `https://<app-name>.scm.chinacloudsites.cn/api/logs/docker` 中检查日志文件。

若要随时停止日志流式处理，请键入 `Ctrl`+`C`。
