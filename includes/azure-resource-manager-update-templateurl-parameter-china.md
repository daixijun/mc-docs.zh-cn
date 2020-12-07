---
title: include 文件
description: include 文件
services: azure-resource-manager
author: rockboyfor
ms.service: azure-resource-manager
ms.topic: include
origin.date: 07/07/2020
ms.date: 11/23/2020
ms.testscope: no
ms.testdate: 09/15/2020
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 5a42552f52cf2a012bf0d3533649c6ef0b9f4436
ms.sourcegitcommit: 054636c134cc0f53c194a6b76668644e18d1c4fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95970648"
---
> [!NOTE]
> 当我们使用以 `https://raw.githubusercontent.com/` 开头的指定模板文件 URI 部署资源时，控制台有时将返回错误，如 `Unable to download deployment content`。
>
> 可以执行以下操作来解决相应问题。
> 1. 下载指定 URI 的模板文件内容并以同一名称另存在本地计算机上。
> 2. 将 `TemplateUri` 的参数替换为 `TemplateFile`，然后用下载的实际文件名更新指定的 URI，并再次运行该脚本。
> 
>    | 语言类别   | 参考链接                   | 操作   |
>    |---         | --------                         |----------|
>    | PowerShell | [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzResourceGroupDeployment) | 将 `-TemplateUri` 替换为 '-TemplateFile` |
>    | Azure CLI  | [az 部署组创建](https://docs.azure.cn/zh-cn/cli/deployment/group#az_deployment_group_create) | 将 `--template-uri` 替换为 '--template-file`|
>