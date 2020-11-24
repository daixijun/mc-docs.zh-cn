---
title: include 文件
description: include 文件
author: rockboyfor
ms.service: cosmos-db
ms.topic: include
ms.date: 11/16/2019
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 75080a2710c97a048cce04728c7b67fe779b173b
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589416"
---
> [!NOTE]
> 当我们从 `https://raw.githubusercontent.com/` 网站下载特定模板文件时，有时会遇到某些问题。
> 
> ![githubusercontent 前缀模板部署问题](./media/azure-raw-githubusercontent-azurechinacloud-environment-notice/githubusercontent_issue.png)
>
> 我们可以按照以下指示修改模板 URI，然后将特定的模板文件下载到本地计算机上。
> 1. 复制模板 URI，通过更改前缀、中缀和模板文件名来转换 URI。
>
>     例如，源 URI 是 `https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-autoscale/azuredeploy.json`
>
>     | 类别 | 原始值 | 转换后的值 |  操作  |
>     |----------|----------------|-----------------|----------|
>     | 前缀   | `https://raw.githubusercontent.com`  |  `https://github.com`  | 更新 |
>     | 中辍    |                | `blob`          |  在 `master` 之前添加，它是 git 存储库的默认分支名称。 |
>     | 模板文件名  |azuredeploy.json | 你的下载模板文件名 | update |
>
>     修改后，转换后的 URI 看起来将类似于 `https://github.com/Azure/azure-quickstart-templates/blob/master/101-cosmosdb-sql-autoscale/azuredeploy.json`。
>     
> 2. 复制转换后的 URI，并在 Internet 浏览器中手动下载特定的模板内容。
> 3. （可选）如果模板文件太大而无法以原始内容的形式显示，我们可以选择带工具提示“`Open this file in GitHub Desktop`”的计算机桌面图标，以便在 GitHub 桌面应用程序中克隆存储库。
