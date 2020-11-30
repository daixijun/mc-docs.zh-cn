---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/17/2020
ms.author: v-junlch
ms.openlocfilehash: c7b0b6911b7626c0d469e19f4517ce371663c8cf
ms.sourcegitcommit: 054636c134cc0f53c194a6b76668644e18d1c4fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95970675"
---
## <a name="clean-up-resources"></a>清理资源

若要继续执行[下一步](#next-steps)并添加 Azure 存储队列输出绑定，请保留所有资源，以备将来使用。

否则，请使用以下命令删除资源组及其包含的所有资源，以免产生额外的费用。

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---

