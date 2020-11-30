---
title: Azure Key Vault 的新增功能
description: Azure Key Vault 的最新更新
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
origin.date: 10/01/2020
ms.date: 11/27/2020
ms.author: v-tawe
ms.openlocfilehash: 8451e305f18cfe15ae78cb4068c105d167ae6758
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300587"
---
# <a name="whats-new-for-azure-key-vault"></a>Azure Key Vault 的新增功能

下面是 Azure Key Vault 的新增功能。 新增功能和改进也在 [Azure 更新 Key Vault 频道](https://azure.microsoft.com/updates/?category=security&query=Key%20vault)上进行了公布。

## <a name="october-2020"></a>2020 年 10 月

> [!WARNING]
> 这些更新可能影响 Azure Key Vault 实现。

为了支持[现在默认情况下启用软删除](#soft-delete-on-by-default)，已对 Azure Key Vault PowerShell cmdlet 进行了两次更改：

- 已将 [Update-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/update-azkeyvault) 的 DisableSoftDelete 和 EnableSoftDelete parameters 参数弃用。
- [Get-AzKeyVaultSecret](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvaultsecret) cmdlet 的输出不再具有 `SecretValueText` 属性。

## <a name="july-2020"></a>2020 年 7 月

> [!WARNING]
> 这两个更新可能影响 Azure Key Vault 实现。

### <a name="soft-delete-on-by-default"></a>Key Vault 软删除在默认情况下开启

到 2020 年年底，软删除在默认情况下将对所有 Key Vault（新的和预先存在的）处于开启状态。 有关此可能的中断性变更的完整详细信息，以及查找受影响的 Key Vault 并预先更新它们的步骤，请参阅[对所有 Key Vault 启用软删除](soft-delete-change.md)。

### <a name="azure-tls-certificate-changes"></a>Azure TLS 证书更改

Microsoft 在将 Azure 服务更新为使用来自一组不同的根证书颁发机构 (CA) 的 TLS 证书。 此更改正在进行中，因为当前 CA 证书不符合某个 CA/浏览器论坛基线要求。  有关完整详细信息，请参阅 [Azure TLS 证书更改](../../security/fundamentals/tls-certificate-changes.md)。

## <a name="june-2020"></a>2020 年 6 月

Azure Monitor for Key Vault 现在为预览版。  Azure Monitor 提供 Key Vault 请求、性能、失败和延迟的统一视图，在其中可以全面监视密钥保管库。 有关详细信息，请参阅 [Azure Monitor for Key Vault（预览版）](../../azure-monitor/insights/key-vault-insights-overview.md)。

<!-- ## May 2020

Key Vault "bring your own key" (BYOK) is now generally available. See the [Azure Key Vault BYOK specification](../keys/byok-specification.md), and learn how to [Import HSM-protected keys to Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md). -->

<!-- ## March 2020

Private endpoints now available in preview. Azure Private Link Service enables you to access Azure Key Vault and Azure hosted customer/partner services over a Private Endpoint in your virtual network.  Learn how to [Integrate Key Vault with Azure Private Link](private-link-service.md). -->

## <a name="2019"></a>2019

- 发布下一代 Azure Key Vault SDK。 有关其用法的示例，请参阅适用于 [Python](../secrets/quick-create-python.md)、[.NET](../secrets/quick-create-net.md)、[Java](../secrets/quick-create-java.md) 和 [Node.js](../secrets/quick-create-node.md) 的 Azure Key Vault 机密快速入门
- 用于管理 Key Vault 证书的新 Azure 策略。 请参阅 [Key Vault 的 Azure Policy 内置定义](../policy-reference.md)。
- Azure Key Vault 虚拟机扩展现已正式发布。  请参阅[适用于 Linux 的 Key Vault 虚拟机扩展](../../virtual-machines/extensions/key-vault-linux.md)和[适用于 Windows 的 Key Vault 虚拟机扩展](../../virtual-machines/extensions/key-vault-windows.md)。

<!-- - Event-driven secrets management for Azure Key Vault now available in Azure Event Grid. For more information, see [the Event Grid schema for events in Azure Key Vault](../../event-grid/event-schema-key-vault.md], and learn how to [Receive and respond to key vault notifications with Azure Event Grid](event-grid-tutorial.md). -->

## <a name="2018"></a>2018

本年度发布的新功能和集成：

- 与 Azure Functions 集成。 有关利用 [Azure Functions](../../azure-functions/index.yml) 进行密钥保管库操作的示例方案，请参阅[自动轮换机密](../secrets/tutorial-rotation.md)。
- [Azure Key Vault 的虚拟网络服务终结点](overview-vnet-service-endpoints.md)。

<!-- - [Integration with Azure Databricks](https://docs.microsoft.com/azure/databricks/scenarios/store-secrets-azure-key-vault). With this, Azure Databricks now supports two types of secret scopes: Azure Key Vault-backed and Databricks-backed. For more information, see [Create an Azure Key Vault-backed secret scope](https://docs.microsoft.com/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope) -->

## <a name="2016"></a>2016

本年度发布的新功能：

- 托管存储帐户密钥。 新增了存储帐户密钥功能，可更轻松地与 Azure 存储集成。 参阅概述主题[托管存储帐户密钥概述](../secrets/overview-storage-keys.md)，了解详细信息。
- 软删除。 软删除功能增强了 Key Vault 和 Key Vault 对象的数据保护。 参阅概述主题[软删除概述](./soft-delete-overview.md)，了解详细信息。

## <a name="2015"></a>2015

本年度发布的新功能：
- 证书管理。 2016 年 9 月 26 日，作为一种功能添加到 2015-06-01 正式版中。

2015 年 6 月 24 日发布了 2015-06-01 正式版。 此版本进行了以下更改：
- 删除密钥 - 删除了“使用”字段。
- 获取有关密钥的信息 - 删除了“使用”字段。
- 将密钥导入保管库 - 删除了“使用”字段。
- 还原密钥 - 删除了“使用”字段。
- 将“RSA_OAEP”改为 RSA 算法的“RSA-OAEP”。 请参阅[关于密钥、机密和证书](about-keys-secrets-certificates.md)。

2015 年 4 月 20 日发布了第二版预览版（版本 2015-02-01-preview）。 有关详细信息，请参阅 [REST API 更新](https://docs.microsoft.com/archive/blogs/kv/rest-api-update) 博客文章。 更新了以下任务：

- 列出保管库中的密钥 - 向操作添加了分页支持。
- 列出密钥版本 - 添加了列出密钥版本的操作。
- 列出保管库中的机密 - 添加了分页支持。
- 列出机密版本 - 添加了列出机密版本的操作。
- 所有操作 - 添加了属性创建/更新时间戳。
- 创建机密 - 向机密添加了 Content-Type。
- 创建密钥 - 添加了标记作为可选信息。
- 创建机密 - 添加了标记作为可选信息。
- 更新密钥 - 添加了标记作为可选信息。
- 更新机密 - 添加了标记作为可选信息。
- 将机密的最大大小从 10 KB 更改为 25 KB。 请参阅[关于密钥、机密和证书](about-keys-secrets-certificates.md)。

## <a name="2014"></a>2014

2015 年 1 月 8 日发布了第一版预览版（版本 2014-12-08-preview）。

## <a name="next-steps"></a>后续步骤

如果你有其他问题，请通过[支持](https://support.azure.cn/)联系我们。  
