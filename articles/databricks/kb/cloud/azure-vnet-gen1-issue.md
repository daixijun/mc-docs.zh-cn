---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 03/10/2020
title: Azure Data Lake Storage Gen1 的网络配置导致 ADLException - 获取文件信息时出错 - Azure Databricks
description: 了解如何解决将 Azure Data Lake Storage Gen1 与 Azure Databricks 配合使用时出现的凭据传递失败问题。
ms.openlocfilehash: f3b3da5b237d92fc250cb5929702924ed834b09b
ms.sourcegitcommit: 16af84b41f239bb743ddbc086181eba630f7f3e8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94589810"
---
# <a name="network-configuration-of-azure-data-lake-storage-gen1-causes-adlexception-error-getting-info-for-file"></a>Azure Data Lake Storage Gen1 的网络配置导致 `ADLException: Error getting info for file`

## <a name="problem"></a>问题

实施以下网络配置时，对 Azure Data Lake Storage Gen1 (ADLS Gen1) 的访问将失败并显示 `ADLException: Error getting info for file <filename>`：

* Azure Databricks 工作区部署在你自己的虚拟网络中（使用 VNet 注入）。
* 通过 Azure Data Lake Storage 凭据传递允许流量。
* 启用 ADLS Gen1 存储防火墙。
* 已为 Azure Databricks 工作区的虚拟网络启用 Azure Active Directory (Azure AD) 服务终结点。

> [!div class="mx-imgBorder"]
> ![no-alternative-text](../_static/images/cloud/azure-gen1-access-issue.png)

## <a name="cause"></a>原因

Azure Databricks 使用位于其自己的虚拟网络中的控制平面，该控制平面负责从 Azure AD 获取令牌。 ADLS 凭据传递使用控制平面获取 Azure AD 令牌，以使用 ADLS Gen1 对交互式用户进行身份验证。

当你在自己的虚拟网络中部署 Databricks 工作区（使用 VNet 注入）时，将在你自己的虚拟网络中创建 Azure Databricks 群集。 为了提高安全性，你可以通过实现 Azure AD 的服务终结点，将 ADLS Gen1 防火墙配置为仅允许来自你自己的虚拟网络的请求，从而限制对 ADLS Gen 1 帐户的访问。

但是，在这种情况下，ADLS 凭据传递会失败。 原因在于，当 ADLS Gen1 检查创建令牌的虚拟网络时，它发现该网络是 Azure Databricks 控制平面，而不是执行原始传递调用的客户提供的虚拟网络。

## <a name="solution"></a>解决方案

要将 ADLS 凭据传递与服务终结点、存储防火墙和 ADLS Gen1 配合使用，请在[防火墙设置](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview#network-isolation)中启用“允许访问 Azure 服务”。

如果你对在防火墙中启用此设置有安全顾虑，可以升级到 ADLS Gen2。 ADLS Gen2 适用于上面所述的网络配置。

有关详细信息，请参阅：

* [在 Azure 虚拟网络中部署 Azure Databricks](/databricks/administration-guide/cloud-configurations/azure/vnet-inject)
* [使用 Azure Active Directory 凭据自动访问 Azure Data Lake Storage](/databricks/data/data-sources/azure/adls-passthrough)