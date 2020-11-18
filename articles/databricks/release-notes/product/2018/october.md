---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 01/16/2020
title: 2018 年 10 月 - Azure Databricks
description: Azure Databricks 新功能和改进的 2018 年 10 月发行说明。
ms.openlocfilehash: dcb049fcfab5b3e89fd24c54227d0e63cc71b93d
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329232"
---
# <a name="october-2018"></a>2018 年 10 月

这些功能和 Azure Databricks 平台的改进已于 2018 年 10 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="copy-notebook-file-path-without-opening-notebook"></a>在不打开笔记本的情况下复制笔记本文件路径

**2018 年 10 月 23 日至 30 日：版本 2.83**

现在，可以通过在文件浏览器中右键单击笔记本名称（或单击笔记本名称右侧的下拉箭头 ![下拉菜单](../../../_static/images/menu-dropdown.png)）并选择“复制文件路径”来复制笔记本文件路径。

> [!div class="mx-imgBorder"]
> ![笔记本复制路径](../../../_static/images/workspace/copy-file-path.png)

## <a name="azure-key-vault-backed-secret-scopes-now-supported-on-standard-plan-accounts"></a>标准计划帐户现在支持 Azure Key Vault 支持的机密范围

**2018 年 10 月 16 日：版本 2.82**

现在，Azure Databricks 允许你使用“创建机密范围”页来指定是让所有用户都对某个机密范围拥有 `MANAGE` 权限，还是仅让机密范围的创建者拥有该权限 。 这使你能够在标准计划中使用 Azure Key Vault 支持的机密范围，而这要求将 `MANAGE` 权限分配给所有用户。

有关详细信息，请参阅[创建 Azure Key Vault 支持的机密范围](../../../security/secrets/secret-scopes.md#akv-ss)。

> [!NOTE]
>
> Azure Key Vault 支持的机密范围处于预览状态。