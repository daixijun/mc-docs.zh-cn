---
title: 快速入门 - 使用 Azure CLI 创建 Azure Key Vault
description: 介绍如何使用 Azure CLI 创建 Azure Key Vault 的快速入门
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
origin.date: 07/20/2020
ms.date: 11/27/2020
ms.author: v-tawe
ms.openlocfilehash: dc82a91b5b0de1c462423ba40dfe818824af68b1
ms.sourcegitcommit: b6fead1466f486289333952e6fa0c6f9c82a804a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2020
ms.locfileid: "96300936"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>快速入门：使用 Azure CLI 创建密钥保管库

Azure Key Vault 是一项云服务，它为[密钥](../keys/index.yml)、[机密](../secrets/index.yml)和[证书](../certificates/index.yml)提供了安全的存储。 有关 Key Vault 的详细信息，请参阅[关于 Azure Key Vault](overview.md)；有关密钥保管库中可存储的内容的详细信息，请参阅[关于密钥、机密和证书](about-keys-secrets-certificates.md)。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

<!-- [!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)] -->

 - 本快速入门需要 Azure CLI 版本 2.0.4 或更高版本。

## <a name="create-a-resource-group"></a>创建资源组

资源组是在其中部署和管理 Azure 资源的逻辑容器。 以下示例在“chinaeast”位置创建名为“ContosoResourceGroup”的资源组。

```azurecli
az group create --name "myResourceGroup" -l "ChinaEast"
```

## <a name="create-a-key-vault"></a>创建 key vault

在上一步骤创建的资源组中创建 Key Vault。 需要提供某些信息：

- Key Vault 名称：由 3 到 24 个字符构成的字符串，只能包含数字 (0-9)、字母（a-z、A-Z）和连字符 (-)

  > [!Important]
  > 每个密钥保管库必须具有唯一的名称。 在以下示例中，将 <your-unique-keyvault-name> 替换为密钥保管库的名称。

- 资源组名称：myResourceGroup。
- 位置：**ChinaEast**。

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "ChinaEast"
```

此 cmdlet 的输出显示新创建的密钥保管库的属性。 请记下下面列出的两个属性：

- **保管库名称**：为上面的 --name 参数提供的名称。
- **保管库 URI**：在本示例中为 https://&lt;your-unique-keyvault-name&gt;.vault.azure.cn/。 通过其 REST API 使用保管库的应用程序必须使用此 URI。

目前，只有你的 Azure 帐户才有权对这个新保管库执行任何操作。

## <a name="clean-up-resources"></a>清理资源

本系列中的其他快速入门和教程是在本快速入门的基础上制作的。 如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。

如果不再需要资源组和所有相关的资源，可以使用 Azure CLI [az group delete](/cli/group) 命令将其删除：

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个 Key Vault 并将其删除。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](overview.md)
- 请参阅 [Azure CLI az keyvault 命令](/cli/keyvault)参考
- 查看 [Azure Key Vault 最佳做法](best-practices.md)
