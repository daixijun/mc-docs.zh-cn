---
title: 使用 Azure Stack Hub 门户部署模板
description: 了解如何使用 Azure Stack Hub 门户部署模板。
author: WenJason
ms.topic: how-to
origin.date: 10/05/2020
ms.date: 11/09/2020
ms.author: v-jay
ms.reviewer: sijuman
ms.lastreviewed: 10/05/2020
ms.openlocfilehash: d0d1139e807a8cd5e05d22d4f4b0196868357430
ms.sourcegitcommit: f187b1a355e2efafea30bca70afce49a2460d0c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330675"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>使用 Azure Stack Hub 门户部署模板

可以使用 Azure Stack Hub 用户门户将 Azure 资源管理器模板部署到 Azure Stack Hub。

## <a name="to-deploy-a-template"></a>部署模板

1. 登录到 Azure Stack Hub 用户门户，选择“+ 创建资源” > “自定义” > “模板部署”  。

   ![在 Azure Stack Hub 门户中创建资源](media/azure-stack-deploy-template-portal/template-deploy1a.png)

2. 可以选择“键入以开始筛选”以选择 GitHub 快速启动模板，或选择“在编辑器中生成自己的模板” 。

   ![在 Azure Stack Hub 门户中部署模板](media/azure-stack-deploy-template-portal/template-deploy2a.png)

    [**AzureStack-QuickStart-Templates**](https://github.com/Azure/AzureStack-QuickStart-Templates) 由 Azure Stack Hub 社区的成员创建，而不由我们创建。 每个模板均按照其所有者而非我们的许可协议授权给你。 我们不对这些模板负责，也不检查其安全性、合规性或性能。 社区模板不受任何 Azure 支持计划或服务的支持，按“原样”提供，且没有任何形式的保证。

3. 如果选择了“在编辑器中生成自己的模板”，请将 JSON 模板代码粘贴到代码窗口中。

   ![在 Azure Stack Hub 门户中编辑模板](media/azure-stack-deploy-template-portal/template-deploy3a.png)

    - 选择“快速启动模板”，在编辑器中加载社区模板。

    - 选择“加载文件”，将 Azure 资源管理器模板从本地计算机加载到编辑器中。

    - 选择“下载”，将 Azure 资源管理器模板保存到本地计算机。

    对模板进行更改后，选择“保存”。

4. 选择“订阅”。 选择要使用的订阅。 选择“资源组”。 可以选择现有资源组，或创建新资源组，然后选择“确定”。 然后选择“查看 + 创建”。

   ![在 Azure Stack Hub 门户中编辑参数](media/azure-stack-deploy-template-portal/template-deploy4a.png)

5. 选择“创建”。

   ![在 Azure Stack Hub 门户中选择订阅](media/azure-stack-deploy-template-portal/template-deploy5a.png)

6. 仪表板上的新磁贴会跟踪模板部署的进度。

   ![在 Azure Stack Hub 门户中选择资源组](media/azure-stack-deploy-template-portal/template-deploy6a.png)

   可使用 Azure 资源管理器 模板通过单个协调操作部署和预配应用程序的所有资源。 还可以重新部署模板，对资源组中的资源进行更改。 有关将模板用于 Azure Stack Hub 的详细信息，请参阅[在 Azure Stack Hub 中使用 Azure 资源管理器模板](azure-stack-arm-templates.md)。

## <a name="next-steps"></a>后续步骤

若要了解有关部署模板的详细信息，请参阅以下文章：

- [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
