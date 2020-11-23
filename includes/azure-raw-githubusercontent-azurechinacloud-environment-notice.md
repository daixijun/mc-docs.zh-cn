---
title: include 文件
description: include 文件
author: rockboyfor
ms.service: cosmos-db
ms.topic: include
ms.date: 11/17/2019
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 03bde7022acbb137710f7f6c4cfd131a58c2d023
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589412"
---
> [!NOTE]
> 当我们从 `https://raw.githubusercontent.com/` 网站下载特定模板并尝试从自定义模板进行部署时，有时会遇到某些问题。
> 
> ![githubusercontent 前缀模板部署问题](./media/azure-raw-githubusercontent-azurechinacloud-environment-notice/githubusercontent_issue.png)
>
> 我们可以按照以下指示在 Azure 中国云环境中部署模板：
> 1. 复制模板 URI，通过更改前缀、中缀和模板文件名来转换 URI。
>     例如，源 URI 是 `https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-autoscale/azuredeploy.json`
>
>     | 类别 | 原始值 | 转换后的值 |  操作  |
>     |----------|----------------|-----------------|----------|
>     | 前缀   | `https://raw.githubusercontent.com`  |  `https://github.com`  | 更新 |
>     | 中辍    |                | `blob`          |  在 `master` 之前添加（Git 存储库的默认分支名称） |
>     | 模板文件名  |azuredeploy.json | 你的下载模板文件名 | update |
>
>     修改后，转换后的 URI 看起来将类似于 `https://github.com/Azure/azure-quickstart-templates/blob/master/101-cosmosdb-sql-autoscale/azuredeploy.json`。
>
> 2. 复制转换后的 URI，并在 Internet 浏览器中手动下载特定的模板内容。
> 3. 在门户的“自定义部署”页中，选择“在编辑器中生成自己的模板”。
> 4. 将下载的模板内容复制到“编辑模板”页中，并按照 Azure 中国云的要求更新参数，例如 Azure 中国云环境不支持的终结点、位置、VM 映像、VM 大小、SKU 和资源提供程序的 API 版本。
> 5. 选择“保存”以返回“自定义部署”页。
> 6. 选择“我同意上述条款和条件”。
> 7. 选择“购买”。
