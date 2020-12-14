---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
origin.date: 07/20/2020
ms.date: 12/11/2020
ms.author: v-tawe
ms.openlocfilehash: eb53828047d1c75098c6822257755f27ab27409b
ms.sourcegitcommit: d8dad9c7487e90c2c88ad116fff32d1be2f2a65d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97104707"
---
本快速入门使用预先创建的 Azure Key Vault。 可以遵循 [Azure CLI 快速入门](../articles/key-vault/general/quick-create-cli.md)、[Azure PowerShell 快速入门](../articles/key-vault/general/quick-create-powershell.md)或 [Azure 门户快速入门](../articles/key-vault/general/quick-create-portal.md)中的步骤创建 Key Vault。 

或者，只需运行以下 Azure CLI 或 Azure PowerShell 命令。

> [!Important]
> 每个密钥保管库必须具有唯一的名称。 在以下示例中，将 <your-unique-keyvault-name> 替换为密钥保管库的名称。

```azurecli
az group create --name "myResourceGroup" -l "ChinaEast"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location ChinaEast

New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "ChinaEast"
```