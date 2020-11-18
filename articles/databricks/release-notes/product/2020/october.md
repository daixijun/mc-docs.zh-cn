---
ms.topic: conceptual
ms.service: azure-databricks
ms.reviewer: mamccrea
ms.custom: databricksmigration
ms.author: saperla
author: mssaperla
ms.date: 10/07/2020
title: 2020 年 10 月 - Azure Databricks
description: Azure Databricks 新功能和改进的 2020 年 10 月发行说明。
ms.openlocfilehash: fba8f3e4d9377d42419334874b14cc6feac5c275
ms.sourcegitcommit: 6b499ff4361491965d02bd8bf8dde9c87c54a9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329207"
---
# <a name="october-2020"></a>2020 年 10 月

这些功能和 Azure Databricks 平台的改进已于 2020 年 10 月发布。

> [!NOTE]
>
> 发布分阶段进行。 在初始发布日期后，可能最长需要等待一周，你的 Azure Databricks 帐户才会更新。

## <a name="render-images-at-higher-retina-resolution-using-matplotlib"></a>使用 matplotlib 在更高的“retina”分辨率下呈现图像

**2020 年 10 月 7 日至 13 日：版本 3.30**

现在可以在 Python 笔记本中以标准分辨率的两倍呈现 matplotlib 图像，为高分辨率屏幕用户提供更好的可视化效果体验。 请参阅 [Matplotlib](../../../notebooks/visualizations/matplotlib.md)。

## <a name="use-the-databricks-cli-or-the-databricks-api-to-create-azure-key-vault-backed-secret-scopes"></a>使用 Databricks CLI 或 Databricks API 创建由 Azure Key Vault 提供支持的机密范围

**2020 年 10 月 7 日至 13 日：版本 3.30**

现在可以使用 Databricks CLI 或 Databricks REST API 创建由 Azure Key Vault 提供支持的机密范围。 请参阅[使用 Databricks CLI 创建 Azure Key Vault 支持的机密范围](../../../security/secrets/secret-scopes.md#create-an-azure-key-vault-backed-secret-scope-using-the-databricks-cli)和[创建机密范围](../../../dev-tools/api/latest/secrets.md#secretsecretservicecreatescope)。

## <a name="use-azure-ad-tokens-to-authenticate-to-the-databricks-cli"></a>使用 Azure AD 令牌向 Databricks CLI 进行身份验证

**2020 年 10 月 7 日至 13 日：版本 3.30**

现在可以使用 Azure Active Directory 令牌向 Databricks CLI 进行身份验证。 请参阅[设置身份验证](../../../dev-tools/cli/index.md#set-up-authentication)。